---
layout: post
title: "#127 The Anatomy of Qwen3-8B: Every Tensor, Matrix and Dimension, Explorable 🔬"
categories: [AI, Software Engineering]
tags: [LLM, Qwen3, Transformer, Architecture, Tensors, Visualization]
difficulty: Medium
---

Qwen3-8B is 399 tensors, 36 layers that look alike and a handful of numbers. This post takes the model apart down to the last matrix: the name of every tensor in the checkpoint, its exact shape, how many numbers it holds, which rows of a matrix belong to which attention head, how the shapes change on their way through one layer, and how rotary position encoding really rotates. Everything is derived from the model's published configuration and checked against the model repository: adding up all tensors reproduces the size of the weight files to the byte.

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
.lq table.lq-t code{font-size:11px}
.lq pre{overflow-x:auto}
</style>

How to use it: every section has a widget. Click, type, drag.

## Contents

1. [Eight numbers define the network](#symbols)
2. [How to read a shape](#shapes)
3. [The checkpoint: 399 tensors (interactive)](#checkpoint)
4. [The blueprint: one forward pass with exact shapes](#blueprint)
5. [The matrices drawn to scale (interactive)](#scale)
6. [One layer, operation by operation (interactive)](#layer)
7. [Attention in exact dimensions: heads, rows and rotations (interactive)](#attention)
8. [Why these numbers: layers, heads and key/value heads (interactive builder)](#why)
9. [Check it yourself](#reproduce)
10. [Limits of this post and sources](#sources)

<a id="symbols"></a>

## 1. Eight numbers define the network

The whole architecture of Qwen3-8B is fixed by a few entries of its `config.json` ([source](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json), read on 2026-10-20). I give them short symbols, because every shape in this post is built from them:

| Symbol | Meaning | Config key | Value |
|---|---|---|---|
| `H` | width of the vector that travels through the network | `hidden_size` | 4,096 |
| `N` | number of layers | `num_hidden_layers` | 36 |
| `n_q` | number of query heads | `num_attention_heads` | 32 |
| `n_kv` | number of key/value heads | `num_key_value_heads` | 8 |
| `d` | size of one head | `head_dim` | 128 |
| `I` | width of the MLP in the middle | `intermediate_size` | 12,288 |
| `V` | rows of the embedding table | `vocab_size` | 151,936 |
| `θ` | base of the rotary position encoding | `rope_theta` | 1,000,000 |

From these follow the derived sizes that appear everywhere:

```
q_dim  = n_q  × d = 32 × 128 = 4,096     (width of the query vector, equals H here)
kv_dim = n_kv × d =  8 × 128 = 1,024     (width of the key vector and of the value vector)
g      = n_q / n_kv = 4                  (query heads that share one key/value head)
I      = 3 × H                           (the MLP is three times as wide as the stream)
V      = 1,187 × 128                     (a multiple of 128, chosen to suit GPU kernels)
```

A few more config entries describe what is *absent*, and that matters for counting tensors:

- `attention_bias: false`: the projections have **no bias vectors**, only weight matrices.
- `tie_word_embeddings: false`: the embedding table and the output head are **two separate matrices**.
- `sliding_window: null`: every token may look at **all** earlier tokens.
- `torch_dtype: bfloat16`: every number is stored in **2 bytes**. `max_position_embeddings` is 40,960 and `rms_norm_eps` is 1e-6.

<a id="shapes"></a>

## 2. How to read a shape

A shape such as `[B, T, 4096]` lists the sizes of the dimensions of a tensor:

- `B` is the **batch**: how many sequences are processed side by side.
- `T` is the number of **tokens** processed in this pass. It is the length of the prompt when the prompt is read, and 1 when one new token is generated.
- `L` is the length of the **keys and values** the attention looks at: the tokens already in the cache plus the `T` new ones.

A **linear layer** multiplies every token vector with a weight matrix. PyTorch stores that matrix as `[out_features, in_features]` and computes

```
y = x @ W.T          with  x: [..., in]   W: [out, in]   y: [..., out]
y[r] = dot( W[r, :], x )          row r of W produces output number r
```

So **rows are outputs and columns are inputs**. `q_proj.weight` has the shape `[4096, 4096]`, `k_proj.weight` has `[1024, 4096]` (1,024 outputs, each reading all 4,096 inputs), and `down_proj.weight` has `[4096, 12288]`. Reshapes (`view`) and transposes only change how the same numbers are indexed, they do not copy or compute anything. Remember the convention: whenever this post says "rows 512 to 639 of `q_proj`", it means 128 output numbers.

<a id="checkpoint"></a>

## 3. The checkpoint: 399 tensors

The weights of the model live in five `safetensors` files, plus a small index (`model.safetensors.index.json`) that maps the name of every tensor to the file that contains it. I read that index from the repository. It lists **399 tensors**, and its metadata says that together they occupy **16,381,470,720 bytes**. The tensors fall into three groups:

- `model.embed_tokens.weight`: the embedding table (1 tensor).
- `model.layers.{i}.…` for `i` = 0 … 35: **11 tensors per layer**, 396 in total.
- `model.norm.weight` and `lm_head.weight`: the final norm and the output head (2 tensors).

The 11 tensors of a layer are four attention matrices, three MLP matrices and four small RMSNorm scale vectors: two for the input of each block and two for the heads (`q_norm` and `k_norm`, 128 numbers each, applied to every query and key head).

Use the explorer to list the tensors of any layer, filter by name, and see the exact shapes, parameter counts, byte sizes and the shard file of each tensor. The sums at the bottom are computed from the shapes, not looked up.

<div class="lq" id="lqG">
<div class="lq-row"><label>Scope <select id="lqG-s"></select></label><label>Filter <input type="text" id="lqG-f" placeholder="e.g. k_proj" style="background:var(--lq-soft);border:1px solid var(--lq-line);border-radius:8px;padding:6px 8px;width:160px;color:inherit;font:inherit"></label></div>
<div id="lqG-t" style="margin-top:6px"></div>
<div id="lqG-sum" class="lq-grid" style="margin-top:12px"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
var H=4096,NL=36,NQ=32,NKV=8,D=128,I=12288,V=151936;
var LT=[
["input_layernorm.weight",[H],"RMSNorm scale in front of attention","input_layernorm"],
["self_attn.q_proj.weight",[NQ*D,H],"queries: 32 bands of 128 rows","q_proj"],
["self_attn.k_proj.weight",[NKV*D,H],"keys: 8 bands of 128 rows","k_proj"],
["self_attn.v_proj.weight",[NKV*D,H],"values: 8 bands of 128 rows","v_proj"],
["self_attn.q_norm.weight",[D],"RMSNorm scale for every query head","q_norm"],
["self_attn.k_norm.weight",[D],"RMSNorm scale for every key head","k_norm"],
["self_attn.o_proj.weight",[H,NQ*D],"mixes the 32 head outputs","o_proj"],
["post_attention_layernorm.weight",[H],"RMSNorm scale in front of the MLP","post_attention_layernorm"],
["mlp.gate_proj.weight",[I,H],"gate branch of the MLP","gate_proj"],
["mlp.up_proj.weight",[I,H],"up branch of the MLP","up_proj"],
["mlp.down_proj.weight",[H,I],"back from 12,288 to 4,096","down_proj"]];
function shard(l,k){if(l<=6)return 1;if(l===7)return (k==="q_proj"||k==="k_proj"||k==="v_proj")?1:2;if(l<=16)return 2;if(l===17)return (k==="input_layernorm"||k==="down_proj"||k==="up_proj"||k==="post_attention_layernorm")?3:2;if(l<=26)return 3;if(l===27)return (k==="input_layernorm"||k==="down_proj"||k==="post_attention_layernorm")?4:3;return 4}
function prod(a){var p=1;a.forEach(function(x){p*=x});return p}
var rows=[{n:"model.embed_tokens.weight",s:[V,H],r:"lookup table: one row per token ID",sh:1,sc:"embedding"}],l;
for(l=0;l<NL;l++)(function(l){LT.forEach(function(x){rows.push({n:"model.layers."+l+"."+x[0],s:x[1],r:x[2],sh:shard(l,x[3]),sc:"layer "+l})})})(l);
rows.push({n:"model.norm.weight",s:[H],r:"final RMSNorm scale",sh:4,sc:"head"},{n:"lm_head.weight",s:[V,H],r:"one score per token ID",sh:5,sc:"head"});
rows.forEach(function(r){r.p=prod(r.s)});
var sel=$("lqG-s"),o='<option value="all">All 399 tensors</option><option value="embedding">Embedding</option>';
for(l=0;l<NL;l++)o+='<option value="layer '+l+'">Layer '+l+'</option>';
sel.innerHTML=o+'<option value="head">Final norm and head</option>';
sel.value="layer 0";
function shp(s){return "["+s.map(nf).join(", ")+"]"}
function render(){
var scope=sel.value,q=$("lqG-f").value.trim().toLowerCase(),list=rows.filter(function(r){return (scope==="all"||r.sc===scope)&&(!q||r.n.toLowerCase().indexOf(q)>=0)});
var p=0;list.forEach(function(r){p+=r.p});
var h='<table class="lq-t"><tr><th>Tensor</th><th>Shape [out, in]</th><th>Parameters</th><th>bf16 bytes</th><th>Shard</th></tr>',shown=list.slice(0,40);
shown.forEach(function(r){h+='<tr><td><code>'+r.n+'</code><div class="lq-sm lq-mut">'+r.r+'</div></td><td><code>'+shp(r.s)+'</code></td><td>'+nf(r.p)+'</td><td>'+nf(r.p*2)+'</td><td>'+r.sh+'</td></tr>'});
h+='<tr style="font-weight:600"><td>'+list.length+' tensor'+(list.length===1?"":"s")+(list.length>shown.length?' (first '+shown.length+' shown)':"")+'</td><td></td><td>'+nf(p)+'</td><td>'+nf(p*2)+'</td><td></td></tr></table>';
$("lqG-t").innerHTML=h;
var tp=0,sh=[0,0,0,0,0,0];rows.forEach(function(r){tp+=r.p;sh[r.sh]+=r.p*2});
var files=["4.00","3.99","3.96","3.19","1.24"],c='<div class="lq-card"><b>Whole checkpoint, computed from the shapes</b><div class="lq-m"><span>Tensors</span><span>'+rows.length+'</span></div><div class="lq-m"><span>Parameters</span><span>'+nf(tp)+'</span></div><div class="lq-m"><span>× 2 bytes (bf16)</span><span>'+nf(tp*2)+' B</span></div><div class="lq-m"><span>total_size in the repository index</span><span>16,381,470,720 B</span></div><div class="lq-m"><span>Match</span><span><b>'+(tp*2===16381470720?"exact":"MISMATCH")+'</b></span></div></div>';
c+='<div class="lq-card"><b>The five shard files</b>';
for(var i=1;i<=5;i++)c+='<div class="lq-m"><span>model-0000'+i+'-of-00005</span><span>'+nf(sh[i])+' B ('+(sh[i]/1e9).toFixed(2)+' GB; listed as '+files[i-1]+' GB)</span></div>';
$("lqG-sum").innerHTML=c+'</div>'}
sel.addEventListener("change",render);$("lqG-f").addEventListener("input",render);
render();
})();
</script>

Two things worth noticing in the table. First, there is no `.bias` tensor anywhere, which is what `attention_bias: false` means in practice. Second, the shard files do not split exactly at layer boundaries: a layer such as layer 7 or 17 is spread over two files, simply because the exporter filled one file up to its size limit and continued in the next.


<a id="blueprint"></a>

## 4. The blueprint: one forward pass with exact shapes

This is the complete path of a batch of token IDs through the network. Boxes show the tensors that hold weights (with the exact shape of the stored matrix), arrows carry the shapes of the data that flows through. The belt on the left is the **residual stream**: the vector `x` of 4,096 numbers per token. Each of the two blocks of a layer reads `x`, computes a correction and adds it back (the circles with a plus).

<div class="lq">
<svg viewBox="0 0 680 1080" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-labelledby="lq-bp-t lq-bp-d" font-size="12">
<title id="lq-bp-t">Blueprint of one Qwen3-8B forward pass with tensor shapes</title>
<desc id="lq-bp-d">Token IDs of shape B by T pass through the embedding table into a stream of shape B, T, 4096. Each of 36 layers applies an input RMSNorm, query, key and value projections, query and key norms, rotary position encoding, grouped-query attention with a key-value cache, an output projection and a residual add, then a second RMSNorm, gate and up projections, a SiLU gating, a down projection and a residual add. A final norm and the output head produce logits of shape B, T, 151936.</desc>
<defs><marker id="lq-bp-a" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="240" y="8" width="200" height="30" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="28" text-anchor="middle" fill="currentColor">input_ids  [B, T]</text>
<line x1="340" y1="38" x2="340" y2="58" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="190" y="60" width="300" height="44" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="79" text-anchor="middle" fill="currentColor" font-weight="600">model.embed_tokens</text>
<text x="340" y="96" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[151,936, 4,096]</text>
<line x1="340" y1="104" x2="340" y2="128" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="350" y="121" fill="currentColor" font-size="11" opacity=".8">x  [B, T, 4,096]</text>
<rect x="16" y="130" width="648" height="730" rx="16" fill="none" stroke="currentColor" stroke-opacity=".5" stroke-dasharray="6 4"/>
<text x="648" y="150" text-anchor="end" fill="currentColor" font-weight="600" font-size="12">model.layers.i, i = 0 … 35  (36 times)</text>
<text x="56" y="148" fill="currentColor" font-size="11" opacity=".8">residual stream x [B, T, 4,096]</text>
<line x1="46" y1="156" x2="46" y2="537" stroke="currentColor" stroke-width="2"/>
<line x1="46" y1="559" x2="46" y2="825" stroke="currentColor" stroke-width="2"/>
<line x1="46" y1="847" x2="46" y2="858" stroke="currentColor" stroke-width="2"/>
<line x1="46" y1="175" x2="228" y2="175" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="230" y="160" width="220" height="30" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="180" text-anchor="middle" fill="currentColor" font-size="11">input_layernorm  [4,096]</text>
<line x1="340" y1="190" x2="340" y2="206" stroke="currentColor" stroke-width="1.5"/>
<line x1="175" y1="206" x2="515" y2="206" stroke="currentColor" stroke-width="1.5"/>
<line x1="175" y1="206" x2="175" y2="220" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<line x1="345" y1="206" x2="345" y2="220" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<line x1="515" y1="206" x2="515" y2="220" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="100" y="222" width="150" height="44" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="175" y="241" text-anchor="middle" fill="currentColor" font-weight="600">q_proj</text>
<text x="175" y="258" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[4,096, 4,096]</text>
<rect x="270" y="222" width="150" height="44" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="345" y="241" text-anchor="middle" fill="currentColor" font-weight="600">k_proj</text>
<text x="345" y="258" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[1,024, 4,096]</text>
<rect x="440" y="222" width="150" height="44" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="515" y="241" text-anchor="middle" fill="currentColor" font-weight="600">v_proj</text>
<text x="515" y="258" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[1,024, 4,096]</text>
<line x1="175" y1="266" x2="175" y2="298" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="183" y="286" fill="currentColor" font-size="11" opacity=".8">[B, T, 32, 128]</text>
<line x1="345" y1="266" x2="345" y2="298" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="353" y="286" fill="currentColor" font-size="11" opacity=".8">[B, T, 8, 128]</text>
<line x1="515" y1="266" x2="515" y2="370" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="523" y="286" fill="currentColor" font-size="11" opacity=".8">[B, T, 8, 128]</text>
<rect x="100" y="300" width="320" height="44" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="260" y="319" text-anchor="middle" fill="currentColor" font-weight="600" font-size="11">q_norm, k_norm  [128] each</text>
<text x="260" y="336" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">then RoPE with cos, sin tables [L, 128]</text>
<line x1="175" y1="344" x2="175" y2="370" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="183" y="362" fill="currentColor" font-size="11" opacity=".8">Q [B, 32, T, 128]</text>
<line x1="345" y1="344" x2="345" y2="370" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="353" y="362" fill="currentColor" font-size="11" opacity=".8">K [B, 8, T, 128]</text>
<rect x="100" y="372" width="490" height="70" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="345" y="392" text-anchor="middle" fill="currentColor" font-weight="600">GQA attention</text>
<text x="345" y="410" text-anchor="middle" fill="currentColor" font-size="11">softmax( Q · Kᵀ ÷ √128 + causal mask ) · V</text>
<text x="345" y="428" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">K, V cache [B, 8, L, 128] each. Query head h reads KV head ⌊h ÷ 4⌋</text>
<line x1="345" y1="442" x2="345" y2="466" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="353" y="459" fill="currentColor" font-size="11" opacity=".8">[B, T, 4,096]</text>
<rect x="245" y="468" width="200" height="40" rx="8" fill="rgba(27,175,122,.2)" stroke="#1baf7a"/>
<text x="345" y="486" text-anchor="middle" fill="currentColor" font-weight="600">o_proj</text>
<text x="345" y="501" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[4,096, 4,096]</text>
<polyline points="345,508 345,548 59,548" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<circle cx="46" cy="548" r="11" fill="rgba(128,128,128,.25)" stroke="rgba(128,128,128,.7)"/>
<text x="46" y="553" text-anchor="middle" fill="currentColor" font-weight="600" font-size="14">+</text>
<line x1="46" y1="589" x2="228" y2="589" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="230" y="574" width="220" height="30" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="594" text-anchor="middle" fill="currentColor" font-size="11">post_attention_layernorm  [4,096]</text>
<line x1="340" y1="604" x2="340" y2="620" stroke="currentColor" stroke-width="1.5"/>
<line x1="235" y1="620" x2="445" y2="620" stroke="currentColor" stroke-width="1.5"/>
<line x1="235" y1="620" x2="235" y2="632" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<line x1="445" y1="620" x2="445" y2="632" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="150" y="634" width="170" height="44" rx="8" fill="rgba(138,123,234,.22)" stroke="#8a7bea"/>
<text x="235" y="653" text-anchor="middle" fill="currentColor" font-weight="600">gate_proj</text>
<text x="235" y="670" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[12,288, 4,096]</text>
<rect x="360" y="634" width="170" height="44" rx="8" fill="rgba(138,123,234,.22)" stroke="#8a7bea"/>
<text x="445" y="653" text-anchor="middle" fill="currentColor" font-weight="600">up_proj</text>
<text x="445" y="670" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[12,288, 4,096]</text>
<line x1="235" y1="678" x2="235" y2="698" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="243" y="693" fill="currentColor" font-size="11" opacity=".8">[B, T, 12,288]</text>
<line x1="445" y1="678" x2="445" y2="698" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="453" y="693" fill="currentColor" font-size="11" opacity=".8">[B, T, 12,288]</text>
<rect x="190" y="700" width="300" height="34" rx="8" fill="rgba(138,123,234,.22)" stroke="#8a7bea"/>
<text x="340" y="721" text-anchor="middle" fill="currentColor" font-size="11">SiLU(gate) ⊙ up   [B, T, 12,288]</text>
<line x1="340" y1="734" x2="340" y2="756" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="240" y="758" width="200" height="40" rx="8" fill="rgba(138,123,234,.22)" stroke="#8a7bea"/>
<text x="340" y="776" text-anchor="middle" fill="currentColor" font-weight="600">down_proj</text>
<text x="340" y="791" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[4,096, 12,288]</text>
<polyline points="340,798 340,836 59,836" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<circle cx="46" cy="836" r="11" fill="rgba(128,128,128,.25)" stroke="rgba(128,128,128,.7)"/>
<text x="46" y="841" text-anchor="middle" fill="currentColor" font-weight="600" font-size="14">+</text>
<line x1="340" y1="860" x2="340" y2="884" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<text x="350" y="878" fill="currentColor" font-size="11" opacity=".8">x  [B, T, 4,096]</text>
<rect x="230" y="886" width="220" height="30" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="906" text-anchor="middle" fill="currentColor" font-size="11">model.norm  [4,096]</text>
<line x1="340" y1="916" x2="340" y2="938" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="190" y="940" width="300" height="44" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="959" text-anchor="middle" fill="currentColor" font-weight="600">lm_head</text>
<text x="340" y="976" text-anchor="middle" fill="currentColor" opacity=".8" font-size="11">[151,936, 4,096]</text>
<line x1="340" y1="984" x2="340" y2="1006" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-bp-a)"/>
<rect x="215" y="1008" width="250" height="30" rx="8" fill="rgba(128,128,128,.14)" stroke="rgba(128,128,128,.6)"/>
<text x="340" y="1028" text-anchor="middle" fill="currentColor" font-size="11">logits  [B, T, 151,936]</text>
<text x="340" y="1062" text-anchor="middle" fill="currentColor" font-size="11" opacity=".75">When generating text, only the last position is needed: [B, 1, 151,936]</text>
</svg>
</div>

Four details in the picture deserve a sentence each.

- **Two residual adds per layer.** The attention output and the MLP output are never written *over* `x`, they are added to it. The stream keeps its shape `[B, T, 4096]` all the way from the embedding to the final norm.
- **Q, K and V are born with different widths.** The query has 32 heads, the key and the value only 8. They meet in the attention step, where every key/value head is read by four query heads.
- **The value does not pass through the norm and RoPE box.** Only the query and the key are normalised per head and rotated. The value goes straight into the attention.
- **The MLP is the wide part.** For a moment the stream swells from 4,096 to 12,288 numbers per token (gate and up), is combined element by element, and is squeezed back by `down_proj`.

<a id="scale"></a>

## 5. The matrices drawn to scale

Numbers like 16,777,216 are hard to feel. Here every matrix of one layer is drawn with its true proportions: **1,024 rows or columns equal 28 pixels**, so the area of a rectangle is proportional to the number of parameters. A matrix is drawn as stored: height = rows = outputs, width = columns = inputs. The thin lines mark bands of 128 (one attention head). Click a matrix to see exactly what its rows and columns mean.

<div class="lq" id="lqH">
<svg id="lqH-svg" viewBox="0 0 680 600" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-label="The seven weight matrices of one Qwen3-8B layer drawn to scale"></svg>
<div id="lqH-d" class="lq-card" style="margin-top:10px"></div>
<div id="lqH-t" style="margin-top:12px"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
var U=28/1024,LAYER=192937984,QP=16777216,EMB=622329856;
var M=[
{id:"q",s:"q_proj",n:"self_attn.q_proj.weight",o:4096,i:4096,x:16,y:44,c:"att",b:"rows",nb:32,what:"Row r produces output number r of the query vector. Rows 128h to 128h+127 are the 128 numbers of query head h (h = 0 … 31). Every row reads all 4,096 input numbers."},
{id:"k",s:"k_proj",n:"self_attn.k_proj.weight",o:1024,i:4096,x:176,y:44,c:"att",b:"rows",nb:8,what:"Rows 128g to 128g+127 are the 128 numbers of key head g (g = 0 … 7). It has a quarter of the rows of q_proj, because four query heads share each key head."},
{id:"v",s:"v_proj",n:"self_attn.v_proj.weight",o:1024,i:4096,x:176,y:112,c:"att",b:"rows",nb:8,what:"Rows 128g to 128g+127 are the 128 numbers of value head g. Same shape as k_proj, and the same sharing: four query heads read each value head."},
{id:"o",s:"o_proj",n:"self_attn.o_proj.weight",o:4096,i:4096,x:336,y:44,c:"att",b:"cols",nb:32,what:"The 4,096 columns are the 32 head outputs side by side: columns 128h to 128h+127 receive the output of head h. Every one of the 4,096 rows is a weighted sum over all heads."},
{id:"gate",s:"gate_proj",n:"mlp.gate_proj.weight",o:12288,i:4096,x:16,y:222,c:"mlp",what:"Each of the 12,288 rows is one hidden feature: a weighted sum of all 4,096 input numbers. The result is passed through SiLU and acts as the gate."},
{id:"up",s:"up_proj",n:"mlp.up_proj.weight",o:12288,i:4096,x:176,y:222,c:"mlp",what:"Same shape as gate_proj. Its 12,288 outputs are the values that the gate lets through, multiplied element by element."},
{id:"down",s:"down_proj",n:"mlp.down_proj.weight",o:4096,i:12288,x:336,y:222,c:"mlp",what:"The only matrix that is wider than tall: each of the 4,096 rows is a weighted sum over all 12,288 hidden features. It brings the stream back to its normal width."}
];
var FILL={att:"rgba(27,175,122,.22)",mlp:"rgba(138,123,234,.24)"},STR={att:"#1baf7a",mlp:"#8a7bea"},cur="q";
function T(x,y,t,o){o=o||{};return '<text x="'+x+'" y="'+y+'" text-anchor="'+(o.a||"middle")+'" fill="currentColor" font-size="'+(o.s||11)+'"'+(o.w?' font-weight="600"':"")+(o.o?' opacity="'+o.o+'"':"")+'>'+t+'</text>'}
function draw(){
var s=T(16,24,"Attention matrices of one layer",{a:"start",w:1,s:12})+T(16,204,"MLP matrices of one layer",{a:"start",w:1,s:12});
M.forEach(function(m){var w=m.i*U,h=m.o*U,on=m.id===cur,band=(m.b==="rows"?h:w)/m.nb;
s+='<g data-m="'+m.id+'" style="cursor:pointer" tabindex="0" role="button" aria-label="Show details of '+m.s+'"><rect x="'+m.x+'" y="'+m.y+'" width="'+w+'" height="'+h+'" rx="3" fill="'+FILL[m.c]+'" stroke="'+STR[m.c]+'" stroke-width="'+(on?3:1)+'"/>';
if(m.b){for(var j=1;j<m.nb;j++){var p=j*band;s+=(m.b==="rows")?'<line x1="'+m.x+'" y1="'+(m.y+p)+'" x2="'+(m.x+w)+'" y2="'+(m.y+p)+'" stroke="currentColor" stroke-opacity=".28"/>':'<line x1="'+(m.x+p)+'" y1="'+m.y+'" x2="'+(m.x+p)+'" y2="'+(m.y+h)+'" stroke="currentColor" stroke-opacity=".28"/>'}}
s+=T(m.x+w/2,m.y+h+13,m.s,{w:1})+T(m.x+w/2,m.y+h+26,"["+nf(m.o)+", "+nf(m.i)+"]",{o:0.8})+'</g>'});
s+=T(480,64,"Scale: 1,024 rows or columns",{a:"start",o:0.85})+T(480,80,"are 28 pixels. Area is",{a:"start",o:0.85})+T(480,96,"proportional to parameters.",{a:"start",o:0.85})+T(480,120,"Thin lines: bands of 128",{a:"start",o:0.85})+T(480,136,"rows or columns, one per",{a:"start",o:0.85})+T(480,152,"attention head.",{a:"start",o:0.85});
$("lqH-svg").innerHTML=s}
function detail(){var m=M.filter(function(x){return x.id===cur})[0],p=m.o*m.i;
$("lqH-d").innerHTML='<b>model.layers.i.'+m.n+'</b><div class="lq-grid" style="margin-top:8px;grid-template-columns:repeat(auto-fit,minmax(170px,1fr))"><div class="lq-mc"><small>Shape [rows = out, columns = in]</small><b>['+nf(m.o)+', '+nf(m.i)+']</b></div><div class="lq-mc"><small>Parameters</small><b>'+nf(p)+'</b></div><div class="lq-mc"><small>Bytes in bf16</small><b>'+nf(p*2)+'</b></div><div class="lq-mc"><small>Share of one layer</small><b>'+(100*p/LAYER).toFixed(1)+'%</b></div><div class="lq-mc"><small>Compared with q_proj</small><b>'+(p/QP).toFixed(2)+' ×</b></div><div class="lq-mc"><small>FLOPs per token</small><b>'+nf(2*p)+'</b></div></div><p style="margin:10px 0 0">'+m.what+'</p>'}
function table(){var R=[["model.embed_tokens (once)",EMB],["lm_head (once)",EMB]],h='<table class="lq-t"><tr><th>Tensor family</th><th>Parameters</th><th>In units of one layer</th></tr>';
M.forEach(function(m){R.push([m.s+" (per layer)",m.o*m.i])});
R.push(["norm scales (per layer: 2 × 4,096 + 2 × 128)",8448]);
R.forEach(function(r){h+="<tr><td><code>"+r[0]+"</code></td><td>"+nf(r[1])+"</td><td>"+(r[1]/LAYER).toFixed(3)+"</td></tr>"});
h+='<tr style="font-weight:600"><td>One layer in total</td><td>'+nf(LAYER+8448)+'</td><td>1.000</td></tr></table>';$("lqH-t").innerHTML=h}
$("lqH-svg").addEventListener("click",function(e){var g=e.target.closest("[data-m]");if(g){cur=g.getAttribute("data-m");draw();detail()}});
$("lqH-svg").addEventListener("keydown",function(e){if(e.key==="Enter"||e.key===" "){var g=e.target.closest&&e.target.closest("[data-m]");if(g){e.preventDefault();cur=g.getAttribute("data-m");draw();detail()}}});
draw();detail();table();
})();
</script>

What the picture shows at a glance:

- **`k_proj` and `v_proj` are slim.** Each has a quarter of the rows of `q_proj`. That is the visible form of grouped-query attention: 8 key/value heads instead of 32.
- **The MLP dominates.** Its three matrices are 78% of the weights of a layer (151.0 M of 192.9 M). The four attention matrices share the remaining 21.7%.
- **`down_proj` is the `gate_proj` turned on its side.** Same number of parameters, rows and columns swapped.
- **The embedding table and the output head** are each worth 3.2 layers. Together with the 36 layers they make up the 8.19 B parameters, and 15% of all parameters sit outside the layers.


<a id="layer"></a>

## 6. One layer, operation by operation

Now the same layer as a table: every operation in the order it runs, with the weight tensor it uses, the exact shapes going in and out, the number of floating-point operations (FLOPs) and the size of its output. Choose how many sequences you process (`B`), how many tokens are already in the cache (`past`) and how many tokens this pass processes (`T`). Then `L = past + T`.

Three presets cover the two situations that matter: reading a prompt (many tokens at once, empty cache) and generating (one new token, long cache).

How the FLOPs are counted: a matrix product costs `2 × (outputs) × (multiply-adds)`, which is exact. The small element-wise operations (norms, RoPE, softmax, SiLU) are rough estimates and marked with `≈`. They are tiny next to the matrix products.

<div class="lq" id="lqI">
<div class="lq-row" style="gap:6px"><button type="button" data-p="1,8000,1">Generate one token (8,000 cached)</button><button type="button" data-p="1,0,8000">Read an 8,000-token prompt</button><button type="button" data-p="16,8000,1">Generate for 16 sequences</button></div>
<div class="lq-row"><label>B <select id="lqI-B"><option>1</option><option>2</option><option>4</option><option>8</option><option>16</option><option>32</option><option>64</option></select></label><label>past <select id="lqI-P"><option value="0">0</option><option value="1000">1,000</option><option value="4000">4,000</option><option value="8000" selected>8,000</option><option value="16000">16,000</option><option value="32000">32,000</option></select></label><label>T <select id="lqI-T"><option value="1" selected>1</option><option value="16">16</option><option value="128">128</option><option value="1024">1,024</option><option value="4096">4,096</option><option value="8000">8,000</option></select></label><label><input type="checkbox" id="lqI-last" checked> output head for the last position only</label></div>
<div id="lqI-t"></div>
<div id="lqI-c" class="lq-grid" style="margin-top:12px"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
function F(x){if(x>=1e12)return (x/1e12).toFixed(2)+" T";if(x>=1e9)return (x/1e9).toFixed(2)+" G";if(x>=1e6)return (x/1e6).toFixed(1)+" M";if(x>=1e3)return (x/1e3).toFixed(1)+" K";return Math.round(x)+""}
function Bt(x){if(x>=1e9)return (x/1e9).toFixed(2)+" GB";if(x>=1e6)return (x/1e6).toFixed(1)+" MB";if(x>=1e3)return (x/1e3).toFixed(1)+" KB";return Math.round(x)+" B"}
function S(a){return "["+a.map(nf).join(", ")+"]"}
function pr(a){var p=1;a.forEach(function(x){p*=x});return p}
var H=4096,Q=32,K=8,D=128,I=12288,V=151936,N=36;
function build(B,T,P){var L=P+T,R=[];
function r(name,note,w,wp,inS,outS,fl,act,est){R.push({name:name,note:note,w:w,wp:wp,inS:inS,outS:outS,fl:fl,act:act,est:est})}
r("input_layernorm","RMSNorm: rescale every token vector",[H],H,S([B,T,H]),S([B,T,H]),4*B*T*H,B*T*H,1);
r("q_proj","queries",[Q*D,H],Q*D*H,S([B,T,H]),S([B,T,Q*D])+" → view "+S([B,T,Q,D]),2*B*T*H*Q*D,B*T*Q*D);
r("k_proj","keys",[K*D,H],K*D*H,S([B,T,H]),S([B,T,K*D])+" → view "+S([B,T,K,D]),2*B*T*H*K*D,B*T*K*D);
r("v_proj","values",[K*D,H],K*D*H,S([B,T,H]),S([B,T,K*D])+" → view "+S([B,T,K,D]),2*B*T*H*K*D,B*T*K*D);
r("q_norm, k_norm","RMSNorm over the 128 numbers of every head",[D],2*D,S([B,T,Q,D])+" and "+S([B,T,K,D]),"same shapes",4*B*T*(Q+K)*D,B*T*(Q+K)*D,1);
r("RoPE","rotate the pairs (i, i+64) by position × θᵢ",null,0,S([B,Q,T,D])+" and "+S([B,K,T,D]),"same shapes; cos, sin "+S([L,D]),3*B*T*(Q+K)*D,B*T*(Q+K)*D,1);
r("KV cache append","write the new keys and values",null,0,S([B,K,T,D])+" ×2",S([B,K,L,D])+" ×2 (the cache)",0,2*B*K*T*D);
r("repeat_kv (GQA)","every KV head serves 4 query heads",null,0,S([B,K,L,D]),S([B,Q,L,D])+" (logical view)",0,0);
r("scores = Q·Kᵀ ÷ √128","one dot product per query head, query token and key token",null,0,S([B,Q,T,D])+" · "+S([B,Q,D,L]),S([B,Q,T,L]),2*B*Q*T*L*D,B*Q*T*L);
r("causal mask, softmax","percentages over the L keys",null,0,S([B,Q,T,L]),S([B,Q,T,L]),5*B*Q*T*L,B*Q*T*L,1);
r("P·V","blend the values with the attention weights",null,0,S([B,Q,T,L])+" · "+S([B,Q,L,D]),S([B,Q,T,D]),2*B*Q*T*L*D,B*Q*T*D);
r("merge heads","transpose and reshape",null,0,S([B,Q,T,D]),S([B,T,Q*D]),0,B*T*Q*D);
r("o_proj","mix the 32 heads",[H,Q*D],H*Q*D,S([B,T,Q*D]),S([B,T,H]),2*B*T*Q*D*H,B*T*H);
r("residual add","x = x + attention output",null,0,S([B,T,H])+" + "+S([B,T,H]),S([B,T,H]),B*T*H,B*T*H);
r("post_attention_layernorm","RMSNorm in front of the MLP",[H],H,S([B,T,H]),S([B,T,H]),4*B*T*H,B*T*H,1);
r("gate_proj","gate branch",[I,H],I*H,S([B,T,H]),S([B,T,I]),2*B*T*H*I,B*T*I);
r("up_proj","up branch",[I,H],I*H,S([B,T,H]),S([B,T,I]),2*B*T*H*I,B*T*I);
r("SiLU(gate) ⊙ up","smooth switch times values, element by element",null,0,S([B,T,I])+" and "+S([B,T,I]),S([B,T,I]),5*B*T*I,B*T*I,1);
r("down_proj","back to the width of the stream",[H,I],H*I,S([B,T,I]),S([B,T,H]),2*B*T*I*H,B*T*H);
r("residual add","x = x + MLP output",null,0,S([B,T,H])+" + "+S([B,T,H]),S([B,T,H]),B*T*H,B*T*H);
return {R:R,L:L}}
function render(){
var B=+$("lqI-B").value,P=+$("lqI-P").value,T=+$("lqI-T").value,last=$("lqI-last").checked,b=build(B,T,P),R=b.R,L=b.L,lf=0,lw=0;
R.forEach(function(x){lf+=x.fl;lw+=x.wp});
var Tl=last?1:T,normF=4*B*Tl*H,headF=2*B*Tl*H*V,total=N*lf+normF+headF;
var h='<table class="lq-t"><tr><th>#</th><th>Operation</th><th>Weight [out, in]</th><th>Shapes in → out</th><th>FLOPs</th><th>Share</th><th>Output</th></tr>';
R.forEach(function(x,i){var sh=lf?100*x.fl/lf:0;
h+='<tr><td>'+(i+1)+'</td><td><code>'+x.name+'</code><div class="lq-sm lq-mut">'+x.note+'</div></td><td>'+(x.w?'<code>'+S(x.w)+(x.name==="q_norm, k_norm"?" ×2":"")+'</code>':'')+'</td><td class="lq-sm"><code>'+x.inS+'</code><br>→ <code>'+x.outS+'</code></td><td>'+(x.est?"≈ ":"")+F(x.fl)+'</td><td style="min-width:70px"><div style="height:8px;border-radius:4px;background:rgba(42,120,214,.75);width:'+Math.max(sh>0?2:0,sh)+'%"></div><span class="lq-sm">'+sh.toFixed(1)+'%</span></td><td>'+Bt(x.act*2)+'</td></tr>'});
h+='<tr style="font-weight:600"><td></td><td>One layer</td><td>'+nf(lw)+' weights</td><td></td><td>'+F(lf)+'</td><td></td><td></td></tr>';
h+='<tr><td></td><td><code>model.embed_tokens</code><div class="lq-sm lq-mut">row lookup, no arithmetic</div></td><td><code>'+S([V,H])+'</code></td><td class="lq-sm"><code>'+S([B,T])+'</code><br>→ <code>'+S([B,T,H])+'</code></td><td>0</td><td></td><td>'+Bt(B*T*H*2)+'</td></tr>';
h+='<tr><td></td><td>× 36 layers</td><td></td><td></td><td>'+F(N*lf)+'</td><td></td><td></td></tr>';
h+='<tr><td></td><td><code>model.norm</code> and <code>lm_head</code><div class="lq-sm lq-mut">'+(last?"last position only":"all positions")+'</div></td><td><code>'+S([V,H])+'</code></td><td class="lq-sm"><code>'+S([B,Tl,H])+'</code><br>→ <code>'+S([B,Tl,V])+'</code></td><td>'+F(normF+headF)+'</td><td></td><td>'+Bt(B*Tl*V*2)+'</td></tr>';
h+='<tr style="font-weight:600"><td></td><td>Whole forward pass</td><td></td><td></td><td>'+F(total)+'</td><td></td><td></td></tr></table>';
$("lqI-t").innerHTML=h;
var w=(8190735360-V*H)*2,kvl=2*B*L*K*D*2,sc=B*Q*T*L*2;
var c='<div class="lq-card"><b>This pass</b><div class="lq-m"><span>Tokens processed (B × T)</span><span>'+nf(B*T)+'</span></div><div class="lq-m"><span>Keys and values per layer (L)</span><span>'+nf(L)+(L>40960?" (above the native 40,960)":"")+'</span></div><div class="lq-m"><span>FLOPs, one layer</span><span>'+F(lf)+'</span></div><div class="lq-m"><span>FLOPs, whole pass</span><span><b>'+F(total)+'</b></span></div><div class="lq-m"><span>FLOPs per processed token</span><span>'+F(total/(B*T))+'</span></div></div>';
c+='<div class="lq-card"><b>Memory touched</b><div class="lq-m"><span>Weight matrices, read once per pass</span><span>'+Bt(w)+'</span></div><div class="lq-m"><span>… independent of B and T</span><span>yes</span></div><div class="lq-m"><span>KV cache after the pass, one layer</span><span>'+Bt(kvl)+'</span></div><div class="lq-m"><span>KV cache after the pass, 36 layers</span><span>'+Bt(kvl*N)+'</span></div><div class="lq-m"><span>Score tensor if it were stored (one layer)</span><span>'+Bt(sc)+'</span></div></div>';
$("lqI-c").innerHTML=c}
["lqI-B","lqI-P","lqI-T","lqI-last"].forEach(function(id){$(id).addEventListener("change",render)});
document.querySelector("#lqI .lq-row").addEventListener("click",function(e){var b=e.target.closest("button[data-p]");if(!b)return;var p=b.getAttribute("data-p").split(",");$("lqI-B").value=p[0];$("lqI-P").value=p[1];$("lqI-T").value=p[2];render()});
render();
})();
</script>

What the table teaches:

- **Where the arithmetic is.** Every matrix product has FLOPs proportional to `B × T`. The two attention products are proportional to `B × T × L`. When you generate one token (`T = 1`) the seven weight matrices dominate. When you read a long prompt (`T = L = 8,000`) the attention products grow with the square of the prompt and take a visibly larger share. The table counts the full `T × L` score matrix. Kernels that skip the masked upper triangle do roughly half of these attention products during a prompt pass.
- **The weights are read once per pass, whatever B and T are.** The memory line "weight matrices, read once per pass" does not change when you change the batch or the number of tokens. Processing more tokens per pass therefore reuses every weight more often. That is the reason a prompt is cheap per token and a single generated token is not.
- **The score tensor is huge in principle.** For an 8,000-token prompt the matrix `[1, 32, 8000, 8000]` would hold 2.05 billion numbers (4.1 GB in bf16) per layer. Attention kernels of the FlashAttention family never write it to memory, they compute it block by block. The row only shows what a naive implementation would have to store.
- **The cache grows by `T` rows per pass.** The append step writes only the new keys and values, `2 × B × T × 8 × 128` numbers per layer.


<a id="attention"></a>

## 7. Attention in exact dimensions: heads, rows and rotations

Attention is the part where names like "head" and "group" stay vague until you see the indices. Two questions have exact answers:

1. **Which rows of `q_proj`, `k_proj` and `v_proj` belong to which head?**
2. **What exactly does RoPE do to the 128 numbers of a head?**

### Which rows belong to which head

The output of `q_proj` is a vector of 4,096 numbers per token. It is cut into 32 consecutive bands of 128: numbers 0 to 127 are query head 0, numbers 128 to 255 are query head 1, and so on. `k_proj` and `v_proj` produce only 1,024 numbers, cut into 8 bands of 128. The link between the two is plain integer division: **query head `h` uses key/value head `⌊h / 4⌋`**.

Move the slider to select a query head. The three bars are the outputs of the three projections, aligned so that the four query heads of a group sit exactly above their shared key/value head.

<div class="lq" id="lqJ">
<div class="lq-row"><label for="lqJ-h" style="min-width:110px">Query head h</label><input type="range" id="lqJ-h" min="0" max="31" value="13"><b id="lqJ-hv" style="min-width:30px;text-align:right"></b></div>
<svg id="lqJ-svg" viewBox="0 0 680 216" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-label="Rows of q_proj, k_proj and v_proj and the head that each band belongs to"></svg>
<div id="lqJ-i" class="lq-card" style="margin-top:10px"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
var PAL=["#2a78d6","#1baf7a","#eb6834","#8a7bea","#eda100","#e87ba4","#00a3bf","#8a8a86"];
function T(x,y,t,o){o=o||{};return '<text x="'+x+'" y="'+y+'" text-anchor="'+(o.a||"middle")+'" fill="currentColor" font-size="'+(o.s||11)+'"'+(o.w?' font-weight="600"':"")+(o.o?' opacity="'+o.o+'"':"")+'>'+t+'</text>'}
function draw(h){var g=Math.floor(h/4),x0=40,bw=600/32,kw=600/8,s=T(40,18,"q_proj output: 4,096 numbers = 32 bands of 128 (one band per query head)",{a:"start",o:0.85}),i,j;
for(i=0;i<32;i++){var gi=Math.floor(i/4),on=i===h;s+='<rect x="'+(x0+i*bw)+'" y="26" width="'+(bw-1.5)+'" height="30" fill="'+PAL[gi]+'" fill-opacity="'+(on?1:(gi===g?0.7:0.28))+'"'+(on?' stroke="currentColor" stroke-width="2"':'')+'/>'}
s+=T(x0+h*bw+bw/2,46,String(h),{s:10});
s+='<polygon points="'+(x0+4*g*bw)+',56 '+(x0+4*g*bw+4*bw-1.5)+',56 '+(x0+(g+1)*kw-2)+',96 '+(x0+g*kw)+',96" fill="'+PAL[g]+'" fill-opacity="0.2"/>';
for(j=0;j<8;j++)s+='<rect x="'+(x0+j*kw)+'" y="96" width="'+(kw-2)+'" height="30" fill="'+PAL[j]+'" fill-opacity="'+(j===g?1:0.28)+'"'+(j===g?' stroke="currentColor" stroke-width="2"':'')+'/>'+T(x0+j*kw+kw/2,116,"KV "+j,{s:11});
s+=T(40,142,"k_proj output: 1,024 numbers = 8 bands of 128 (one band per key/value head)",{a:"start",o:0.85});
for(j=0;j<8;j++)s+='<rect x="'+(x0+j*kw)+'" y="152" width="'+(kw-2)+'" height="30" fill="'+PAL[j]+'" fill-opacity="'+(j===g?1:0.28)+'"'+(j===g?' stroke="currentColor" stroke-width="2"':'')+'/>'+T(x0+j*kw+kw/2,172,"KV "+j,{s:11});
s+=T(40,198,"v_proj output: same layout as k_proj",{a:"start",o:0.85});
s+=T(28,46,"q",{a:"end",s:12,w:1})+T(28,116,"k",{a:"end",s:12,w:1})+T(28,172,"v",{a:"end",s:12,w:1});
$("lqJ-svg").innerHTML=s}
function info(h){var g=Math.floor(h/4),qa=h*128,ka=g*128;
$("lqJ-i").innerHTML='<b>Query head '+h+' reads key/value head '+g+'</b><div class="lq-grid" style="grid-template-columns:repeat(auto-fit,minmax(200px,1fr));margin-top:8px"><div class="lq-mc"><small>q_proj rows (its query)</small><b>'+nf(qa)+' … '+nf(qa+127)+'</b></div><div class="lq-mc"><small>k_proj and v_proj rows (shared)</small><b>'+nf(ka)+' … '+nf(ka+127)+'</b></div><div class="lq-mc"><small>Query heads sharing KV head '+g+'</small><b>'+(4*g)+', '+(4*g+1)+', '+(4*g+2)+', '+(4*g+3)+'</b></div><div class="lq-mc"><small>o_proj columns that receive head '+h+'</small><b>'+nf(qa)+' … '+nf(qa+127)+'</b></div></div><pre style="margin:10px 0 0;white-space:pre-wrap;font-size:12px"><code>Q_h    = q[:, :, '+h+', :]        # [B, T, 128]   rows '+qa+'..'+(qa+127)+' of q_proj\nK_g    = k_cache[:, '+g+', :, :]     # [B, L, 128]   rows '+ka+'..'+(ka+127)+' of k_proj, for every cached token\nV_g    = v_cache[:, '+g+', :, :]     # [B, L, 128]\nscores = Q_h @ K_g.transpose(-1,-2) / sqrt(128)     # [B, T, L]\nout_h  = softmax(scores + mask) @ V_g               # [B, T, 128] → columns '+qa+'..'+(qa+127)+' of o_proj</code></pre>'}
function upd(){var h=+$("lqJ-h").value;$("lqJ-hv").textContent=h;draw(h);info(h)}
$("lqJ-h").addEventListener("input",upd);upd();
})();
</script>

Some exact facts that follow from the picture:

- **Cache layout.** Per layer the cache holds keys and values of shape `[B, 8, L, 128]` each. One token adds `2 × 8 × 128` numbers, which is 4,096 bytes in bf16, or 147,456 bytes over the 36 layers.
- **Sharing is just indexing.** Reference implementations expand the 8 cached heads to 32 by repeating each one four times in a row (heads 0, 0, 0, 0, 1, 1, 1, 1, …). Fast kernels skip the copy and simply read KV head `⌊h / 4⌋`.
- **What multi-head attention would cost.** With 32 key/value heads, `k_proj` and `v_proj` would be `[4096, 4096]` instead of `[1024, 4096]` and the cache four times larger.
- **The causal mask.** Query token `t` (at position `past + t`) may only look at keys with an index up to `past + t`. All other scores are set to minus infinity before the softmax, so they get exactly zero weight. During generation (`T = 1`) the single query sees every cached key, so the mask changes nothing.

### How the rotation works

Before the keys are stored and before queries meet keys, RoPE rotates the 128 numbers of each head. The numbers are paired **half a head apart**: number `i` is paired with number `i + 64`, for `i` = 0 … 63. Each pair is treated as a point in a plane and turned by the angle

```
φ(p, i) = p × θᵢ        with   θᵢ = 1,000,000 ^ ( −i / 64 )        p = position of the token

a′ =  a·cos φ − b·sin φ        (a, b) = (x[i], x[i + 64])
b′ =  a·sin φ + b·cos φ
```

So every token position turns pair `i` by `p × θᵢ` radians. Pair 0 turns fastest, by exactly 1 radian per position. Pair 63 turns about one millionth of that. The result is a clock with 64 hands at very different speeds. Because rotations compose, the dot product of a rotated query and a rotated key depends only on the **distance** between the two tokens, which is why the model can use position without storing absolute positions anywhere.

Pick a frequency and a position. The circle shows where the hand points, the bars show the wavelength (the number of positions per full turn) of all 64 pairs on a logarithmic scale.

<div class="lq" id="lqK">
<div class="lq-row"><label for="lqK-i" style="min-width:150px">Frequency index i</label><input type="range" id="lqK-i" min="0" max="63" value="0"><b id="lqK-iv" style="min-width:30px;text-align:right"></b></div>
<div class="lq-row"><label for="lqK-p" style="min-width:150px">Token position p</label><input type="range" id="lqK-p" min="0" max="40959" value="3"><b id="lqK-pv" style="min-width:60px;text-align:right"></b></div>
<svg id="lqK-svg" viewBox="0 0 680 250" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-label="A rotating hand for the chosen frequency and the wavelengths of all 64 frequencies"></svg>
<div id="lqK-i2" class="lq-card" style="margin-top:10px"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
function th(i){return Math.pow(1e6,-i/64)}
function T(x,y,t,o){o=o||{};return '<text x="'+x+'" y="'+y+'" text-anchor="'+(o.a||"middle")+'" fill="currentColor" font-size="'+(o.s||11)+'"'+(o.w?' font-weight="600"':"")+(o.o?' opacity="'+o.o+'"':"")+'>'+t+'</text>'}
function hgt(i){return Math.max(1,(Math.log10(2*Math.PI/th(i))-0.7)/6.1*150)}
function upd(){
var i=+$("lqK-i").value,p=+$("lqK-p").value,t=th(i),tau=2*Math.PI,wl=tau/t,phi=(p*t)%tau,c=Math.cos(phi),sn=Math.sin(phi),cx=110,cy=120,R=80,k,s="";
$("lqK-iv").textContent=i;$("lqK-pv").textContent=nf(p);
s+='<circle cx="'+cx+'" cy="'+cy+'" r="'+R+'" fill="none" stroke="currentColor" stroke-opacity=".4"/><line x1="'+(cx-R)+'" y1="'+cy+'" x2="'+(cx+R)+'" y2="'+cy+'" stroke="currentColor" stroke-opacity=".2"/><line x1="'+cx+'" y1="'+(cy-R)+'" x2="'+cx+'" y2="'+(cy+R)+'" stroke="currentColor" stroke-opacity=".2"/>';
s+='<line x1="'+cx+'" y1="'+cy+'" x2="'+(cx+R)+'" y2="'+cy+'" stroke="currentColor" stroke-opacity=".5" stroke-dasharray="3 3"/><line x1="'+cx+'" y1="'+cy+'" x2="'+(cx+R*c)+'" y2="'+(cy-R*sn)+'" stroke="#eb6834" stroke-width="3" stroke-linecap="round"/><circle cx="'+(cx+R*c)+'" cy="'+(cy-R*sn)+'" r="4" fill="#eb6834"/>';
s+=T(cx,22,"pair ("+"x["+i+"], x["+(i+64)+"])",{w:1,s:12})+T(cx,222,"angle φ = "+phi.toFixed(3)+" rad = "+(phi*180/Math.PI).toFixed(1)+"°",{o:0.9})+T(cx,238,"dashed: position 0",{o:0.7});
var bx=250,bw=400/64,base=210;
for(k=0;k<64;k++)s+='<rect x="'+(bx+k*bw)+'" y="'+(base-hgt(k))+'" width="'+(bw-1)+'" height="'+hgt(k)+'" fill="'+(k===i?"#eb6834":"#2a78d6")+'" fill-opacity="'+(k===i?1:0.55)+'"/>';
var ry=base-(Math.log10(40960)-0.7)/6.1*150;
s+='<line x1="'+bx+'" y1="'+ry+'" x2="'+(bx+400)+'" y2="'+ry+'" stroke="currentColor" stroke-dasharray="4 3" stroke-opacity=".7"/>'+T(bx,ry-6,"native context: 40,960 positions",{a:"start",o:0.85});
s+=T(bx,22,"wavelength of each of the 64 pairs (positions per full turn, log scale)",{a:"start",o:0.85})+T(bx,226,"pair 0: 6.3 positions",{a:"start",o:0.8})+T(bx+400,226,"pair 63: 5.1 million",{a:"end",o:0.8});
$("lqK-svg").innerHTML=s;
var slow=0;for(k=0;k<64;k++)if(tau/th(k)>40960)slow++;
var a=0.6,b=0.8;
$("lqK-i2").innerHTML='<div class="lq-grid" style="grid-template-columns:repeat(auto-fit,minmax(200px,1fr))"><div class="lq-mc"><small>θ'+i+' = 1,000,000^(−'+i+'/64)</small><b>'+t.toExponential(3)+' rad per position</b></div><div class="lq-mc"><small>Wavelength 2π ÷ θ</small><b>'+(wl<1e4?wl.toFixed(1):nf(wl))+' positions</b></div><div class="lq-mc"><small>cos φ and sin φ</small><b>'+c.toFixed(4)+' and '+sn.toFixed(4)+'</b></div><div class="lq-mc"><small>Example pair (0.60, 0.80) becomes</small><b>('+(a*c-b*sn).toFixed(3)+', '+(a*sn+b*c).toFixed(3)+')</b></div></div><p style="margin:10px 0 0" class="lq-sm">'+slow+' of the 64 pairs have a wavelength above 40,960 positions: within the native context they never complete even one turn. They tell the model about large distances. The fastest pairs tell it about neighbours. The cos and sin tables hold the 64 angles twice, once for each half of the head, so their shape is [L, 128].</p>'}
$("lqK-i").addEventListener("input",upd);$("lqK-p").addEventListener("input",upd);upd();
})();
</script>


<a id="why"></a>

## 8. Why these numbers: layers, heads and key/value heads

With the exact shapes in hand, the questions behind the numbers can be asked precisely.

**What is a "hidden layer"?** A neural network has an input (here: token IDs), an output (here: a score for every dictionary entry) and a lot of computation in between. Everything in between is called *hidden*, because nobody reads those intermediate vectors directly. In Qwen3-8B the hidden layers are the 36 blocks `model.layers.0` … `model.layers.35`, and `hidden_size` is the width of the vector that travels between them. The config counts blocks, although every block itself contains several smaller steps.

**Why 36 layers?** It is a design choice, not a law of nature. One layer does one round of "gather information from the other tokens" (attention) followed by one round of "think about it" (MLP). Stacking layers lets later rounds build on the results of earlier ones: depth buys *sequential steps of refinement*. The exact number follows from the size budget. The weights of one layer are

```
attention:  q_proj H² + o_proj H² + k_proj ¼H² + v_proj ¼H²  =  2.5 H²
MLP:        gate 3H² + up 3H² + down 3H²                     =  9   H²
per layer:  11.5 × 4,096² = 192,937,984   (plus 8,448 norm weights)

total:      36 × 192,937,984  +  2 × 151,936 × 4,096   =   6.95 B + 1.24 B   =   8.19 B
```

The same budget could be spent on fewer, wider layers or on more, narrower ones. The builder below shows how steeply the layer count changes with the width. I cannot tell you why the designers chose exactly this point.

**What is an attention head?** One independent attention reader. The 4,096 query numbers are cut into 32 bands of 128 (section 7), and each band computes its own scores and its own softmax, so each head can focus on different earlier tokens. Splitting is free in arithmetic: 32 × 128 = 4,096, the same matrix, just indexed in bands. Studies of transformer models in general found heads that follow the previous word, heads that copy repeated text and heads that match a number to its label. I did not verify what the heads of Qwen3-8B specifically do.

**Why only 8 key/value heads?** Memory. Keys and values of all earlier tokens are kept in the cache, so their size is set by the number of key/value heads:

```
bytes per token  =  2 (K and V) × 36 layers × n_kv × 128 × 2 bytes
n_kv = 32 (classic multi-head attention):  589,824 bytes  →  4.7 GB for 8,000 tokens
n_kv =  8 (Qwen3-8B):                      147,456 bytes  →  1.2 GB for 8,000 tokens
n_kv =  1 (multi-query attention):          18,432 bytes  →  0.15 GB for 8,000 tokens
```

Grouped-query attention, the design with 1 < `n_kv` < `n_q`, was introduced as a middle way: the paper reports quality close to multi-head attention at a speed comparable to multi-query attention, while pure multi-query attention can cost quality. Eight also divides 32 evenly and is a common head count when a model is split across eight GPUs, but I cannot verify that this was Qwen's reason.

### Assemble it yourself

Press **Build it** to watch the network being put together piece by piece: first the embedding table, then layer after layer, finally the output head, while the parameter counter climbs towards 8.19 B. Then change the design. The tower is drawn with **width = hidden size** and **height = number of layers**. Its layers are split into an attention part (green) and an MLP part (purple) by their share of the weights, and the dashed outline is Qwen3-8B for comparison.

Things to try:

- Keep the parameter count near 8.19 B and change the hidden size. The card "layers needed for 8.19 B" tells you how deep the tower would have to be.
- Raise the number of layers and watch the **KV cache per token** grow in proportion.
- Raise the KV heads up to the number of query heads (classic multi-head attention) and see the cache explode.

<div class="lq" id="lqF">
<div class="lq-row"><label for="lqF-L" style="min-width:70px">Layers</label><input type="range" id="lqF-L" min="4" max="80" value="36"><b id="lqF-Lv" style="min-width:34px;text-align:right"></b></div>
<div class="lq-row"><label>Hidden size <select id="lqF-H"><option value="2048">2,048</option><option value="3072">3,072</option><option value="4096" selected>4,096</option><option value="5120">5,120</option><option value="6144">6,144</option><option value="8192">8,192</option></select></label><label>KV heads <select id="lqF-K"></select></label><span id="lqF-q" class="lq-sm lq-mut"></span></div>
<div class="lq-row"><button type="button" id="lqF-b">▶ Build it</button><button type="button" id="lqF-r">Reset to Qwen3-8B</button></div>
<div class="lq-grid" style="grid-template-columns:repeat(auto-fit,minmax(290px,1fr));align-items:start"><div id="lqF-svg"></div><div id="lqF-m"></div></div>
<div class="lq-sm lq-mut" style="margin-top:8px">Fixed in this builder: head_dim 128, intermediate_size = 3 × hidden_size, vocabulary 151,936, separate embedding and output head, no biases.</div>
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
function tower(c){var cx=170,sc=300/8192,w=c.H*sc,ref=4096*sc,HT=Math.max(c.L,40)*6+86,yb=HT-12,lh=5,st=stage;
function ok(i){return st<0||st-1>=i}
var s='<svg viewBox="0 0 340 '+HT+'" width="100%" style="max-width:340px;display:block;margin:0 auto" role="img" aria-label="Tower of layers: width is hidden size, height is the number of layers">';
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
var tok=512*c.L*c.kv,need=(TARGET-2*c.emb)/c.per;
var pct=Math.min(100,100*built/TARGET);
var m='<div class="lq-card"><div class="lq-m"><span>Parameters built</span><span><b>'+B(built)+'</b></span></div><div class="lq-bar" style="height:12px;margin:4px 0 10px"><div style="width:'+pct+'%;background:#2a78d6"></div></div>'+
row("Target (Qwen3-8B)","8.19 B")+row("Weights per layer",B(c.per))+row("Total parameters",B(c.total))+row("Size in bf16",GB(c.total*2))+row("Weight FLOPs per token",(2*(c.L*c.per+c.emb)/1e9).toFixed(1)+" GFLOPs")+row("KV cache per token",nf(tok)+" B")+row("KV cache per user at 8,000",GB(tok*8000))+row("Layers needed for 8.19 B at this width","≈ "+(need>0?need.toFixed(0):"none, embeddings alone are too big"))+'</div>';
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

- **Width versus depth at the same size.** About 8.19 B parameters can be reached with roughly 22 layers at width 5,120, 36 at 4,096 or about 66 at 3,072 (if the other ratios are kept). Qwen's designers picked one point on this line.
- **Depth grows the cache.** Doubling the layers doubles the KV cache per token.
- **Width costs weights quadratically.** One layer has about 11.5 × hidden² weights, so a 25% wider model has about 56% more weights per layer.
- **Heads are free, KV heads are not.** Changing the number of query heads (which follows the hidden size at head size 128) does not change the arithmetic. Changing the number of KV heads changes the cache directly.

<a id="reproduce"></a>

## 9. Check it yourself

Everything in this post can be verified on your own machine. The first snippet reads the shape of every tensor from a `safetensors` file without loading a single weight. The format starts with 8 bytes that give the length of a JSON header, and the header lists every tensor with its dtype and shape.

```python
import json, struct

def read_header(path):
    with open(path, "rb") as f:
        n = struct.unpack("<Q", f.read(8))[0]       # length of the JSON header
        return json.loads(f.read(n))

h = read_header("model-00005-of-00005.safetensors")
for name, t in h.items():
    if name != "__metadata__":
        print(name, t["dtype"], t["shape"])         # lm_head.weight BF16 [151936, 4096]
```

The second snippet loads the model and counts the tensors and parameters. (Newer versions of `transformers` call the argument `dtype` instead of `torch_dtype`.)

```python
from transformers import AutoModelForCausalLM

m = AutoModelForCausalLM.from_pretrained("Qwen/Qwen3-8B", torch_dtype="auto")
params = list(m.named_parameters())
print(len(params), sum(p.numel() for _, p in params))      # 399  8190735360

layer = m.model.layers[0]
print(layer.self_attn.q_proj.weight.shape)                  # torch.Size([4096, 4096])
print(layer.self_attn.k_proj.weight.shape)                  # torch.Size([1024, 4096])
print(layer.mlp.down_proj.weight.shape)                     # torch.Size([4096, 12288])

W = layer.self_attn.q_proj.weight
head_13 = W[13 * 128 : 14 * 128]                            # the 128 rows of query head 13: [128, 4096]
```

<a id="sources"></a>

## 10. Limits of this post and sources

**What I checked against the repository (2026-10-20):**

- The values of `config.json`.
- The names of all 399 tensors and their grouping into five files, from `model.safetensors.index.json`.
- The total size of 16,381,470,720 bytes from the same index. It equals 8,190,735,360 parameters × 2 bytes, which is exactly the sum of the shapes used in this post. The five computed file sizes (4.00, 3.99, 3.96, 3.19 and 1.24 GB) agree with the file listing.

**What follows from the config but I did not read directly:**

- The shape of each individual tensor. I did not open the file headers. Because the sum of all shapes reproduces the total byte count exactly, a wrong shape would have to be compensated by another wrong shape, which is unlikely. The snippets in section 9 settle it in a minute.
- The order and the details of the operations (norm of query and key before RoPE, the pairing of number `i` with `i + 64`, query head `h` reading key/value head `⌊h / 4⌋`). They follow the Hugging Face `transformers` implementation of Qwen3 as I know it. Other inference engines may store weights in a permuted layout, but they compute the same function.
- The vocabulary has 151,936 rows in the embedding table. As far as I know the tokenizer itself defines somewhat fewer tokens and the table is padded up to a multiple of 128.

**What is an estimate:** the FLOPs of the element-wise operations (norms, RoPE, softmax, SiLU) are rough counts, marked with `≈`. The matrix products are exact.

**What is not known:** *why* the designers chose 36 layers, 8 key/value heads or a 3× wider MLP, and what the individual heads and layers of this model do. Statements about this come from research on transformer models in general.

**Sources**

- Model configuration: [Qwen3-8B `config.json`](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json)
- Weight index: [`model.safetensors.index.json`](https://huggingface.co/Qwen/Qwen3-8B/blob/main/model.safetensors.index.json) and the [file listing](https://huggingface.co/Qwen/Qwen3-8B/tree/main) of the repository
- The file format: [safetensors](https://github.com/huggingface/safetensors)
- Grouped-query attention: Ainslie et al., [GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints](https://arxiv.org/abs/2305.13245) (EMNLP 2023)
- The transformer: Vaswani et al., [Attention Is All You Need](https://arxiv.org/abs/1706.03762)
- Rotary position embedding: Su et al., [RoFormer](https://arxiv.org/abs/2104.09864)
- RMSNorm: Zhang and Sennrich, [Root Mean Square Layer Normalization](https://arxiv.org/abs/1910.07467)
- SwiGLU: Shazeer, [GLU Variants Improve Transformer](https://arxiv.org/abs/2002.05202)
