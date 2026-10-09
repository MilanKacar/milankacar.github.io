---
layout: post
title: "#127 CUDA Indexing Explained: Threads, Blocks and Grids You Can Play With 🧩"
categories: [AI, Software Engineering, GPU]
tags: [CUDA, GPU, Parallel Programming, Visualization, Algorithms]
difficulty: Medium
---

CUDA indexing is the part of GPU programming that confuses almost everyone at the start. Below you will find a school analogy, the one formula you need, and **two interactive visualizations** so you can see how threads, blocks and the grid fit together.

## The problem: 1,000 apples, 1,000 helpers

Imagine you have 1,000 apples to wash and you hire 1,000 helpers. You give all of them the *same* instruction: "wash an apple". If that is all you say, every helper grabs apple number 1.

So every helper needs to answer one question: **which apple is mine?**

That is all CUDA indexing is. A GPU kernel is one piece of code that thousands of threads run at the same time, and each thread uses its own index to pick its own piece of data.

## The cast: a school

| CUDA term | School analogy | Built-in variable |
|---|---|---|
| Thread | one kid | `threadIdx` (seat number inside the classroom) |
| Block | one classroom | `blockIdx` (classroom number inside the school) |
| Grid | the whole school | `gridDim` (how many classrooms) |
| Threads per block | kids per classroom | `blockDim` |

The catch: **seat numbers restart at 0 in every classroom.** Seat 2 exists in classroom 0, classroom 1 and classroom 2. If every kid used only their seat number, three kids would grab the same apple.

The fix is a school-wide ID:

> **ID = (classroom number × kids per classroom) + seat number**

In CUDA code that is the famous line:

```cuda
int i = blockIdx.x * blockDim.x + threadIdx.x;
```

<style>
.cudaviz{--text-primary:#1f1f1e;--text-secondary:#5f5e5a;--text-success:#3b6d11;--text-warning:#854f0b;--text-danger:#a32d2d;--surface-1:#f5f4ed;--border-strong:rgba(0,0,0,.3);--border-danger:#e24b4a;--bg-danger:#fcebeb;--radius:8px;--font-mono:ui-monospace,SFMono-Regular,Menlo,Consolas,monospace;color:var(--text-primary);margin:1.5rem 0;max-width:100%}
.cudaviz.dark{--text-primary:#ececea;--text-secondary:#a8a69e;--text-success:#97c459;--text-warning:#ef9f27;--text-danger:#f09595;--surface-1:#2c2c2a;--border-strong:rgba(255,255,255,.3);--border-danger:#a32d2d;--bg-danger:#501313}
.cudaviz .cv-row{display:flex;align-items:center;gap:12px;margin:0 0 8px;font-size:13px;color:var(--text-secondary)}
.cudaviz .cv-row label{min-width:190px}
.cudaviz .cv-row input{flex:1}
.cudaviz .cv-row b{min-width:24px;text-align:right;font-weight:600;color:var(--text-primary)}
.cudaviz .cv-lbl{font-size:12px;color:var(--text-secondary);margin:0 0 6px}
.cudaviz .cv-readout{background:var(--surface-1);border-radius:var(--radius);padding:12px 14px;margin:12px 0 16px;font-family:var(--font-mono);font-size:13px;line-height:1.7;color:var(--text-primary);word-break:break-word}
.cudaviz .cv-blk{border:1px solid;border-radius:8px;padding:8px}
.cudaviz .cv-t{width:42px;height:46px;border-radius:6px;border:1px solid;display:flex;flex-direction:column;align-items:center;justify-content:center;font-size:12px;line-height:1.4;cursor:pointer;box-sizing:border-box;color:var(--text-primary)}
.cudaviz .cv-d{width:30px;height:30px;border-radius:4px;border:1px solid;display:flex;align-items:center;justify-content:center;font-size:12px;box-sizing:border-box;color:var(--text-primary)}
.cudaviz .cv-cell{width:100%;height:42px;border-radius:5px;border:1px solid;display:flex;flex-direction:column;align-items:center;justify-content:center;font-size:12px;line-height:1.3;cursor:pointer;box-sizing:border-box;color:var(--text-primary)}
</style>

## Visualization 1: 1D indexing

Each colored box is a **block**, each small square is a **thread**. The top number of a square is `threadIdx.x` (it restarts in every block), the bottom number is the global index `i`. The row at the bottom is your data array.

**How to play:**

- Hover (or tap) a thread to see the formula and which array element it works on.
- Move **numBlocks** down until some array cells turn dashed red. Those elements have no thread and will silently never be processed.
- Move **n** below the thread count. The extra threads fade out. They are idle because of the bounds check.

<div class="cudaviz" markdown="0">
<div>
<div class="cv-row"><label>threadsPerBlock (blockDim.x)</label><input type="range" id="cu1-tpb" min="2" max="8" step="1" value="4"><b id="cu1-tpb-o">4</b></div>
<div class="cv-row"><label>numBlocks (gridDim.x)</label><input type="range" id="cu1-nb" min="1" max="5" step="1" value="3"><b id="cu1-nb-o">3</b></div>
<div class="cv-row"><label>n (array length)</label><input type="range" id="cu1-nn" min="1" max="40" step="1" value="10"><b id="cu1-nn-o">10</b></div>
</div>
<div class="cv-readout" id="cu1-readout"></div>
<p class="cv-lbl">Grid: each box is a block, each square is a thread (top = threadIdx.x, bottom = global i)</p>
<div id="cu1-grid" style="display:flex;flex-wrap:wrap;gap:10px;margin:0 0 16px"></div>
<p class="cv-lbl" id="cu1-dlbl"></p>
<div id="cu1-data" style="display:flex;flex-wrap:wrap;gap:4px"></div>
<script>
(function(){
var cols=['#7F77DD','#1D9E75','#D85A30','#378ADD','#BA7517'];
var sel={b:1,t:2};
function el(id){return document.getElementById(id)}
function render(){
var T=+el('cu1-tpb').value,B=+el('cu1-nb').value,N=+el('cu1-nn').value;
el('cu1-tpb-o').textContent=T;el('cu1-nb-o').textContent=B;el('cu1-nn-o').textContent=N;
if(sel.b>=B)sel.b=B-1;
if(sel.t>=T)sel.t=T-1;
var g='';
for(var b=0;B>b;b++){
var c=cols[b%5];
g+='<div class="cv-blk" style="border-color:'+c+'"><div class="cv-lbl">Block '+b+'</div><div style="display:flex;gap:4px">';
for(var t=0;T>t;t++){
var i=b*T+t,idle=(i>=N),s=(b===sel.b&&t===sel.t);
var st='background:'+c+(s?'99':'22')+';border-color:'+c+';';
if(s)st+='border-width:2px;';
if(idle)st+='opacity:.4;border-style:dashed;';
g+='<div class="cv-t" data-b="'+b+'" data-t="'+t+'" style="'+st+'"><span>t='+t+'</span><span style="font-weight:600">i='+i+'</span></div>';
}
g+='</div></div>';
}
el('cu1-grid').innerHTML=g;
var selI=sel.b*T+sel.t;
var d='';
for(var k=0;N>k;k++){
var bb=Math.floor(k/T);
var covered=(B>bb);
var st2;
if(covered){
var c2=cols[bb%5];
st2='background:'+c2+(k===selI?'99':'22')+';border-color:'+c2+';';
if(k===selI)st2+='border-width:2px;';
}else{
st2='border-color:var(--border-danger);border-style:dashed;background:var(--bg-danger);';
}
d+='<div class="cv-d" style="'+st2+'">'+k+'</div>';
}
el('cu1-data').innerHTML=d;
el('cu1-dlbl').textContent='Data array a[0..'+(N-1)+'] (dashed red = no thread assigned)';
var r='<div>i = blockIdx.x * blockDim.x + threadIdx.x</div>';
r+='<div>  = '+sel.b+' * '+T+' + '+sel.t+' = <b>'+selI+'</b></div>';
if(N>selI){
r+='<div style="color:var(--text-success)">'+selI+' &lt; n ('+N+'): thread works on a['+selI+']</div>';
}else{
r+='<div style="color:var(--text-warning)">'+selI+' &gt;= n ('+N+'): bounds check fails, thread does nothing</div>';
}
var total=B*T,need=Math.ceil(N/T);
if(N>total){
r+='<div style="color:var(--text-danger)">Only '+total+' threads for '+N+' elements. Need ceil(n/T) = '+need+' blocks.</div>';
}else if(total>N){
r+='<div style="color:var(--text-secondary)">'+(total-N)+' extra thread(s) are idle. Blocks needed: ceil('+N+'/'+T+') = '+need+'</div>';
}else{
r+='<div style="color:var(--text-secondary)">Perfect fit: '+total+' threads for '+N+' elements.</div>';
}
el('cu1-readout').innerHTML=r;
}
function pick(e){
var x=e.target.closest('.cv-t');
if(!x)return;
var b=+x.getAttribute('data-b'),t=+x.getAttribute('data-t');
if(b===sel.b&&t===sel.t)return;
sel={b:b,t:t};
render();
}
el('cu1-grid').addEventListener('mouseover',pick);
el('cu1-grid').addEventListener('click',pick);
['cu1-tpb','cu1-nb','cu1-nn'].forEach(function(id){el(id).addEventListener('input',render)});
render();
})();
</script>
</div>

### What you just saw

- `threadIdx.x` is **local**: it restarts at 0 in every block.
- The global index `i` is **unique**: it keeps counting across blocks.
- Every block has the same size, so the last block is often not completely needed. That is where idle threads come from.

## What if the sizes do not match?

Say you have **45 apples** but launch **5 blocks × 5 threads = 25 threads**. Threads get IDs 0 to 24, so apples 25 to 44 are never touched. There is **no error message**, your program just returns a half-finished result. In the visualization above, those are the dashed red cells.

### Fix 1: launch enough threads

Round **up** when computing the number of blocks:

```cuda
int threads = 5;
int blocks  = (n + threads - 1) / threads;   // ceil(45 / 5) = 9
kernel<<<blocks, threads>>>(a, b, out, n);
```

If it does not divide evenly (say 23 apples with 5 threads per block gives 5 blocks = 25 threads), the last two threads have no apple. Those two must do nothing, which is the job of the bounds check:

```cuda
__global__ void add(const float* a, const float* b, float* out, int n) {
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if (i < n) {                 // threads with i >= n sit out
        out[i] = a[i] + b[i];
    }
}
```

Without `if (i < n)` the extra threads read and write memory outside your array. That can corrupt other data or crash, often silently. Idle threads are cheap, out-of-bounds access is not.

### Fix 2: grid-stride loop

If you cannot (or do not want to) launch one thread per element, let each thread take several elements. After finishing one, it jumps ahead by the **total number of threads**:

```cuda
__global__ void add(const float* a, const float* b, float* out, int n) {
    int i      = blockIdx.x * blockDim.x + threadIdx.x;
    int stride = gridDim.x * blockDim.x;       // total threads in the grid
    for (; i < n; i += stride) {
        out[i] = a[i] + b[i];
    }
}
```

With 25 threads and 45 elements, thread 0 handles elements 0 and 25, thread 1 handles 1 and 26, and so on. Every element is processed exactly once, and the kernel works no matter how many threads you launch.

## Visualization 2: 2D indexing (images and matrices)

For an image, one number is not enough: each thread needs a **column** and a **row**. It is the same formula, applied twice:

```cuda
int col = blockIdx.x * blockDim.x + threadIdx.x;
int row = blockIdx.y * blockDim.y + threadIdx.y;
int idx = row * width + col;       // plain row-major array indexing
```

Here the image is 8 pixels wide and 6 tall, split into 4×3 thread blocks, so the grid is 2×2 blocks. Each pixel shows its memory index and, in small text, `(threadIdx.x, threadIdx.y)`. Hover over any pixel.

<div class="cudaviz" markdown="0">
<div class="cv-readout" id="cu2-ro" style="margin-top:0"></div>
<p class="cv-lbl">Image is 8 wide x 6 tall, blockDim = (4, 3), gridDim = (2, 2). Each pixel shows its memory index (row * width + col).</p>
<div id="cu2-g" style="display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px;max-width:520px"></div>
<script>
(function(){
var cols2=['#7F77DD','#1D9E75','#D85A30','#378ADD'];
var W=8,BX=4,BY=3;
var s2={bx:1,by:0,tx:2,ty:1};
function el(id){return document.getElementById(id)}
function draw(){
var g='';
for(var by=0;2>by;by++){
for(var bx=0;2>bx;bx++){
var c=cols2[by*2+bx];
g+='<div style="border:1px solid '+c+';border-radius:8px;padding:8px"><div class="cv-lbl">Block (x='+bx+', y='+by+')</div><div style="display:grid;grid-template-columns:repeat('+BX+',minmax(0,1fr));gap:3px">';
for(var ty=0;BY>ty;ty++){
for(var tx=0;BX>tx;tx++){
var col=bx*BX+tx,row=by*BY+ty,idx=row*W+col;
var s=(bx===s2.bx&&by===s2.by&&tx===s2.tx&&ty===s2.ty);
var st='background:'+c+(s?'99':'22')+';border-color:'+c+';';
if(s)st+='border-width:2px;';
g+='<div class="cv-cell" data-bx="'+bx+'" data-by="'+by+'" data-tx="'+tx+'" data-ty="'+ty+'" style="'+st+'"><span style="font-weight:600">'+idx+'</span><span style="font-size:11px;color:var(--text-secondary)">('+tx+','+ty+')</span></div>';
}
}
g+='</div></div>';
}
}
el('cu2-g').innerHTML=g;
var col2=s2.bx*BX+s2.tx,row2=s2.by*BY+s2.ty,idx2=row2*W+col2;
var r='<div>col = blockIdx.x * blockDim.x + threadIdx.x = '+s2.bx+' * '+BX+' + '+s2.tx+' = <b>'+col2+'</b></div>';
r+='<div>row = blockIdx.y * blockDim.y + threadIdx.y = '+s2.by+' * '+BY+' + '+s2.ty+' = <b>'+row2+'</b></div>';
r+='<div>index = row * width + col = '+row2+' * '+W+' + '+col2+' = <b>'+idx2+'</b></div>';
r+='<div style="color:var(--text-secondary)">Small text on each pixel = (threadIdx.x, threadIdx.y) inside its block</div>';
el('cu2-ro').innerHTML=r;
}
function pick(e){
var x=e.target.closest('.cv-cell');
if(!x)return;
var n={bx:+x.getAttribute('data-bx'),by:+x.getAttribute('data-by'),tx:+x.getAttribute('data-tx'),ty:+x.getAttribute('data-ty')};
if(n.bx===s2.bx&&n.by===s2.by&&n.tx===s2.tx&&n.ty===s2.ty)return;
s2=n;
draw();
}
el('cu2-g').addEventListener('mouseover',pick);
el('cu2-g').addEventListener('click',pick);
draw();
})();
</script>
</div>

### What to look for

- **Neighbors in x are neighbors in memory.** Hover along a row and the index goes 8, 9, 10, 11 (+1 per step). Hover down a column and it jumps by 8, the image width. That is why `threadIdx.x` should map to the **column**: consecutive threads then read consecutive addresses (coalesced access), which is much faster.
- **The CUDA part is only `col` and `row`.** `row * width + col` is ordinary row-major array indexing.
- **`threadIdx` is local.** Pixel 6 in block (1,0) has `threadIdx = (2,1)` but global column 6. Block (0,0) also has a thread with `threadIdx.x = 2`, and it works on a different pixel.

## Threads are jobs, cores are hands

A common question: *is the number of threads the number of (tensor) cores?* No.

- **Threads** are the jobs **you** ask for in `<<<blocks, threads>>>`. You can launch millions.
- **Cores** are the actual hardware, fixed by the chip. If you launch more threads than cores, the extra threads simply wait their turn.

Example with an NVIDIA T4:

| Hardware part | What it is | T4 count |
|---|---|---|
| SM (streaming multiprocessor) | The "classroom building" that your blocks are assigned to | 40 |
| CUDA cores | Regular cores, one scalar operation at a time | 2,560 (64 per SM) |
| Tensor cores | Special units that multiply small matrices in one step | 320 (8 per SM) |

Inside an SM, threads run in groups of 32 called **warps**. Tensor cores do not change the indexing formula. You normally use them through libraries (cuBLAS, PyTorch) rather than through plain per-thread code.

## Cheat sheet

| Question | Answer |
|---|---|
| Which element am I? | `i = blockIdx.x * blockDim.x + threadIdx.x` |
| 2D version | `col` from x, `row` from y, then `row * width + col` |
| How many blocks do I need? | `(n + threads - 1) / threads` |
| Why `if (i < n)`? | The last block usually has extra threads that must sit out |
| More data than threads? | Grid-stride loop: `i += gridDim.x * blockDim.x` |
| `threadIdx` vs `i`? | `threadIdx` restarts in every block, `i` is globally unique |

Ask yourself two questions in every kernel: **"Which element am I?"** and **"Is that element actually valid?"** Everything else is arithmetic.
