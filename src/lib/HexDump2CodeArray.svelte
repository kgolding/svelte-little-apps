<script>
  let inp = `		0030   00 fe fb f7 00 00 0a 37 35 32 34 30 30 39 42 22   .......7524009B"
		0040   2a 53 49 41 2d 44 43 53 22 30 30 30 31 52 42 4c   *SIA-DCS"0001RBL
		0050   36 46 23 30 30 31 31 31 31 5b 38 39 46 35 31 44   6F#001111[89F51D
		0060   46 30 35 34 37 35 37 39 38 34 33 32 38 31 36 35   F054757984328165
		0070   32 42 30 30 42 38 30 32 31 46 43 45 41 33 38 45   2B00B8021FCEA38E
		0080   31 44 46 35 37 42 37 35 44 43 34 30 43 41 44 39   1DF57B75DC40CAD9
		0090   45 43 43 32 45 39 42 34 33 37 44 44 33 44 31 34   ECC2E9B437DD3D14
		00a0   39 36 42 39 37 37 32 38 36 39 44 31 32 38 33 33   96B9772869D12833
		00b0   30 44 32 35 46 43 38 38 38 41 43 33 42 31 34 43   0D25FC888AC3B14C
		00c0   41 41 45 36 31 43 36 43 35 37 46 39 46 34 34 43   AAE61C6C57F9F44C
		00d0   44 31 30 33 38 33 36 31 46 33 0d                  D1038361F3.`;

  let lang = "go";

  const process = (s, l) => {
    s = ` ${s} `; // Add dummy boundaries at start & end
    let x = [];

    // Helpers
    const isHex = (x) => {
      return "0123456789abcdefABCDEF".indexOf(x) > -1;
    };

    const isBoundary = (x) => {
      return "\r\n ".indexOf(x) > -1;
    };

    // Loop around the input looking at 4 chars, being
    // Boundary/Hex/Hex/Boundary and when matched pushing them on ret
    for (let i = 2; i < s.length; i++) {
      if (isBoundary(s[i - 2]) && isHex(s[i - 1]) && isHex(s[i])) {
        if (i + 1 == s.length || isBoundary(s[i + 1])) {
          x.push(s.slice(i - 1, i + 1));
        }
      }
    }

    // Format output
    let ret = "";
    let close = "";
    switch (l) {
      case "go":
        ret = "buf := []byte{";
        close = "\n}\n";
        break;

      case "js":
        ret = "const buf = [";
        close = "\n]\n";
        break;
    }
    for (let i = 0; i < x.length; i++) {
      if (i % 8 == 0) {
        ret += " ";
      }
      if (i % 16 == 0) {
        ret += "\n\t";
      }
      ret += `0x${x[i]},`;
    }

    return ret + close;
  };

  const copyToClipboard = () => {
    navigator.clipboard.writeText(out);
  };

  $: out = process(inp, lang);
</script>

<h3>Paste hex dump</h3>
<textarea bind:value={inp} rows="12" class="form-control"></textarea>

<div class="row mt-3">
  <div class="col">
    <h3>Output</h3>
  </div>
  <div class="col-auto">
    <select bind:value={lang} class="form-select">
      <option value="go">Golang</option>
      <option value="js">Javascript</option>
    </select>
  </div>
  <div class="col-auto">
    <button class="btn btn-secondary" on:click={copyToClipboard}>Copy</button>
  </div>
</div>
<textarea bind:value={out} rows="12" readonly class="form-control"></textarea>

<style>
  textarea {
    font-family: "Courier New", Courier, monospace !important;
  }
</style>
