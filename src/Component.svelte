<script>
  import { getContext } from "svelte";
  import * as XLSX from "xlsx/xlsx.mjs";
  export let dataProvider;
  export let columns;
  export let filename;

  $: cleanedFilename = filename ?? "output.xlsx";
  $: dpRows = dataProvider?.rows ?? [];
  $: cleanDpRows(dpRows);

  function getDynamicProps(obj, props) {
      return props.reduce((result, prop) => {
          if (obj.hasOwnProperty(prop)) {
              result[prop] = obj[prop];
          }
          return result;
      }, {});
  }

  function cleanDpRows() {
    let cleanedDpRows = [];
    console.log(columns)
    if (columns??[].length > 0) {
      console.log('still got here')
      dpRows.forEach((row) => {
        const columnNameArray = columns.map(item => item.name);
        const extractedProps = getDynamicProps(row, columnNameArray);
        cleanedDpRows.push(extractedProps)
      });
      return cleanedDpRows
    } else {

      // cleanedDpRows = dpRows.map((x) => x);;
      return dpRows
    }
  }


  function export_data() {
    var ws = XLSX.utils.json_to_sheet(cleanDpRows());
    var wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "data");
    XLSX.writeFile(wb, cleanedFilename);
  }

  const { styleable } = getContext("sdk");
  const component = getContext("component");
</script>

<div use:styleable={$component.styles}>
  <button style="background: transparent; border: none !important; font-size:0;"on:click={export_data}>
    <slot />
  </button>
</div>
