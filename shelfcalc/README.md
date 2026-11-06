# Box rack cut calculator

A single self-contained page (`index.html`, no dependencies) that turns box size, rows × columns, and clearances into a cut list, a shopping list with board-by-board cutting diagrams, runner heights, the spacer size and upright length, and a to-scale front view.

## Embedding

Host the folder anywhere (it's static), then:

```html
<iframe id="shelfcalc" src="/shelfcalc/" style="width:100%;border:0;height:1400px" title="Box rack calculator"></iframe>
<script>
  // The page posts its height so the iframe can fit it without a scrollbar.
  window.addEventListener("message", function (e) {
    var frame = document.getElementById("shelfcalc");
    if (e.source === frame.contentWindow && e.data && e.data.type === "shelfcalc:height")
      frame.style.height = e.data.height + "px";
  });
</script>
```

To preload the inputs, add a hash, e.g. `#spacer=6.755`. "Copy link to this build" generates one, and only the values that differ from the defaults go into it.

Dark mode follows the reader's system setting. To force a theme, set `data-theme="light"` or `"dark"` on the page's `<html>`. A same-origin parent page with its own theme switcher can set it on `frame.contentDocument.documentElement`.

## Model

- Ladders = columns + 1, each a front and back upright. The upright's thin face runs across the rack width.
- Runners lie flat, 2 per box, and span from the front upright to the back upright. Runner length = box depth − front overhang − back overhang.
- Tapered boxes: there are two support styles.
  - **Bottom:** uprights clear the top (widest) width. The bottom must still rest on the runner ledges with the box shoved to one side. If it doesn't, the page suggests a wider runner.
  - **Rim (hung):** the body drops between the runners and the lid lip rests on them. Bay = max(top width + play, body width + play + 2 × runner width). The runner sits beside the box below the lid, so the row pitch is box height + headroom, without the runner thickness.
- Plywood shelves (optional, replaces the runners): one panel per box, bay wide by runner length deep, pocket-screwed to the uprights at the corners. The panel thickness takes the place of the runner thickness in the row pitch, and rim mode is turned off. Panels are laid out on 4×8 sheets as a grid in whichever orientation fits more, with rotated panels added in the leftover strip.
- Spacer = box height + headroom (rim mode: pitch − runner thickness), unless a measured spacer is entered.
- Upright length = top box top + headroom + top tie width, rounded up.
- Build direction: **top down** (the default) references everything from the upright's top end. The top block = tie width + headroom + box height above the runner. The spacer goes above each new runner, so the pocket screws pull the runner into it. Any slack from rounding the uprights up ends up at the floor. **Bottom up** uses a base block from the floor, and the slack ends up above the top tie.
- Width = (columns + 1) × upright thickness + columns × bay.
- Stock lengths: the global checkboxes apply to every size. Under "Different lengths for some sizes", any size in use can get its own list, saved in the link as `stk=1x4:96.144`.
- Cut plan: first-fit-decreasing for each enabled stock length. It keeps the length that wastes the least, then drops each board to the shortest stock that still holds its cuts.
