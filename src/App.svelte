<script lang="ts">
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import { escape } from "svelte/internal";

  let _div: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  let div = _div as unknown | SVGElement;
  let ul: d3.Selection<HTMLUListElement, unknown, HTMLElement, any>;

  onMount(() => {
    _div = d3.select("div");

    let div0 = _div.append("div");
    div0
      .attr("id", "drop_zone")
      .text("ここにファイルをドロップ")
      .on("dragover", function (event) {
        handleDragOver(event);
      })
      .on("drop", function (event) {
        handleDragFileDrop(event);
      });

    let output = _div.append("output");
    output.attr("id", "file_list2");
    ul = _div.append("ul");
  });

  function handleDragOver(event: DragEvent) {
    event.stopPropagation();
    event.preventDefault();
    event.dataTransfer.dropEffect = "copy";
  }

  function handleDragFileDrop(event: DragEvent) {
    console.log(event);
    event.stopPropagation();
    event.preventDefault();

    Array.from(event.dataTransfer.files).forEach((f) => {
      let li = ul.append("li");
      li.append("strong").text(escape(f.name));
      let span = li.append("span");
      span.text(
        " (" +
          (f.type ? f.type : "n/a") +
          ") - " +
          f.size +
          " bytes, last modified: " +
          (f as any).lastModifiedDate.toLocaleDateString()
      );
    });
  }
</script>

<div bind:this={div} class="example" />

<style lang="scss">
  .example {
    :global(#drop_zone) {
      border: 2px dashed #bbb;
      border-radius: 5px;
      padding: 25px;
      text-align: center;
      font: 20pt bold "Vollkorn";
      color: #bbb;
    }
  }
</style>
