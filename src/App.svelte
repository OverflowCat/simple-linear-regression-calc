<script lang="ts">
  import Katex from "svelte-katex";
  let x_src = "100 200 300 400 500 600",
    y_src = "0.383 0.420 0.455 0.490 0.524 0.559",
    xs = [],
    ys = [],
    xys = [],
    x_sqs = [],
    y_sqs = [],
    k = 0,
    x_avg = 0,
    y_avg = 0,
    xy_avg = 0,
    x_sq_avg = 0,
    y_sq_avg = 0,
    b = 0,
    a = 0,
    r = 0,
    ua = 0,
    ub = 0;
  $: {
    xs = x_src
      .replace(/ +/g, " ")
      .trim()
      .split(" ")
      .map((x) => Number(x));
    ys = y_src
      .replace(/ +/g, " ")
      .trim()
      .split(" ")
      .map((y) => Number(y));
    x_sqs = xs.map((x) => x ** 2);
    y_sqs = ys.map((x) => x ** 2);
    xys = xs.map((x, i) => ys[i] * x);
    k = xs.length;
    x_avg = sum(xs) / k;
    y_avg = sum(ys) / k;
    xy_avg = sum(xys) / k;
    x_sq_avg = sum(x_sqs) / k;
    y_sq_avg = sum(y_sqs) / k;
    b = (x_avg * y_avg - xy_avg) / (x_avg ** 2 - x_sq_avg);
    a = y_avg - b * x_avg;
    r =
      (xy_avg - x_avg * y_avg) /
      Math.sqrt((x_sq_avg - x_avg ** 2) * (y_sq_avg - y_avg ** 2));
    ub = b * Math.sqrt((1 / (k - 2)) * (1 / r ** 2 - 1));
    ua = Math.sqrt(x_sq_avg) * ub;
  }
  function sum(arr: number[]) {
    return arr.reduce((partialSum, a) => partialSum + a, 0);
  }
  function trimEndZeros(x: number | string) {
    return String(x).replace(/\.?0+$/, "");
  }
</script>

<h1><b>一元线性回归法</b>实验数据计算器</h1>
<main>
  <div>
    <input bind:value={x_src} />
  </div>
  <div>
    <input bind:value={y_src} />
  </div>
  {#if xs.length == ys.length}
    <table>
      <tbody>
        <tr>
          <td><Katex>x</Katex></td>
          {#each xs as x}
            <td>{x}</td>
          {/each}
        </tr>
        <tr>
          <td><Katex>y</Katex></td>
          {#each ys as y}
            <td>{y}</td>
          {/each}
        </tr>
        <tr>
          <td><Katex>xy</Katex></td>
          {#each xys as xy}
            <td>{trimEndZeros(xy.toFixed(5))}</td>
          {/each}
        </tr>
        <tr>
          <td><Katex>x^2</Katex></td>
          {#each x_sqs as x_sq}
            <td>{trimEndZeros(x_sq.toFixed(5))}</td>
          {/each}
        </tr><tr>
          <td><Katex>y^2</Katex></td>
          {#each y_sqs as y_sq}
            <td>{trimEndZeros(y_sq.toFixed(5))}</td>
          {/each}
        </tr>
      </tbody>
    </table>
    <div class="processing">
      <h3>平均数计算</h3>
      <p>
        <Katex>\bar x = {x_avg}, \bar y = {y_avg.toFixed(5)}</Katex>
      </p>
      <p>
        <Katex
          >\overline x^2 = {(x_avg ** 2).toFixed(5)}, \overline y^2 = {(
            y_avg ** 2
          ).toFixed(5)}</Katex
        >
      </p>
      <p>
        <Katex
          >\overline {"{"}x^2{"}"} = {x_sq_avg.toFixed(5)}, \overline {"{"}y^2{"}"} = {y_sq_avg.toFixed(5)}</Katex
        >
      </p>
      <h3>回归系数</h3>
      <Katex displayMode>
        {`\\begin{align*}`}
        b &= \frac {"{"}\bar x \bar y - \overline{"{"}xy{"}"}
        {"}"}
        {"{"}\overline x^2 - \overline {"{"}x^2{"}"}{"}"} &= {b.toFixed(9)}, \\a &= \bar
        y-b\bar x &= {a.toFixed(9)}.
        {`\\end{align*}`}
      </Katex>
      <Katex displayMode />
      <h3>相关系数</h3>
      <Katex displayMode>
        {`r=\\frac{\\overline{x y}-\\bar{x} \\bar{y}}{\\sqrt{\\left(\\overline{x^{2}}-\\bar{x}^{2}\\right)\\left(\\overline{y^{2}}-\\bar{y}^{2}\\right)}}=`}{r.toFixed(9)}.
      </Katex>
      <h3>不确定度</h3>
      <Katex displayMode>
        {`\\begin{align*}`}
        {`u(b) =& b\\sqrt{\\frac{1}{k-2}(\\frac{1}{r^2-1})} &=`}{ub.toFixed(8)},\\{`u(a) =& \\sqrt{\\overline{x^2}} \\cdot u(b)&=`}{ua.toFixed(8)}.
        {`\\end{align*}`}
      </Katex>
    </div>
  {:else}
    <p><Katex>x, y</Katex>数据个数不一致。</p>
  {/if}
</main>

<style>
  input {
    font-family: monospace;
    width: 80%;
  }
  table {
    font-family: monospace;
    border-collapse: separate;
    border-spacing: 12px 4px;
  }
  td {
    text-align: right;
  }
  .processing {
    text-align: left;
  }
</style>
