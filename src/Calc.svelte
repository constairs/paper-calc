<script>
  export let item;

  export let addToFavorite;
  export let updateItem;

  $: totalTons = parseFloat((+item.listsCount * (+item.itemDensity * +item.itemSize) / 10_000_000_000).toPrecision(4));

  let desityLabels = {
    220: '0.22 кг/м2',
    240: '0.24 кг/м2',
    260: '0.26 кг/м2',
    270: '0.27 кг/м2',
    280: '0.28 кг/м2',
    295: '0.295 кг/м2',
    300: '0.30 кг/м2',
    330: '0.33 кг/м2',
    350: '0.35 кг/м2',
    380: '0.38 кг/м2',
    610: '0.61 кг/м2',
  };

  let sizesLabels = {
    [47 * 65]: '47 x 65 см',
    [62 * 94]: '62 x 94 см',
    [70 * 100]: '70 x 100 см',
    [72 * 102]: '72 x 102 см',
    [72 * 104]: '72 x 104 см',
  };

  let densityList = Object.entries(desityLabels).map(a => ({
    value: a[0],
    label: a[1],
  }));

  let sizes = Object.entries(sizesLabels).map(a => ({
    value: a[0],
    label: a[1],
  }));

  const clickCopyBtn = () => {
    navigator.clipboard.writeText(`${totalTons}`)
    .then()
    .catch();
  };

  const clickAddToFavorite = () => {
    updateItem({
      ...item,
      totalTons,
      favourite: true,
    });

    addToFavorite({
      ...item,
      totalTons,
      itemSizeLabel: sizesLabels[item.itemSize],
      favourite: true,
    });
  };
</script>

<div class="calc-item">
  <div class="calc-item-field">
    <label for="listsCount">Кол-во листов</label>
    <input
      type="text"
      id={`listsCount-${item.id}`}
      bind:value={item.listsCount}
      />
  </div>

  <fieldset class="calc-item-field">
    {#each densityList as density, idx}
      <div class="radio-item">
        <input
          type="radio"
          value={density.value}
          bind:group={item.itemDensity}
          name={`density-${item.id}`}
          id={`density-${item.id}-${idx}`}
        />
        <label for={`density-${item.id}-${idx}`}>{density.label}</label>
      </div>
    {/each}
  </fieldset>

  <fieldset class="calc-item-field">
    {#each sizes as size, idx}
      <div class="radio-item">
        <input
          type="radio"
          value={size.value}
          bind:group={item.itemSize}
          name={`size-${item.id}`}
          id={`size-${item.id}-${idx}`}
        />
        <label for={`size-${item.id}-${idx}`}>{size.label}</label>
      </div>
    {/each}
  </fieldset>

  <div class="calc-item-field">
    {#if totalTons}
      <div class="total">{totalTons} тонн</div>
      <div>
        <button on:click={clickCopyBtn}>Скопировать</button>
        <button on:click={clickAddToFavorite}>
          В избранное
        </button>
      </div>
    {/if}
  </div>
</div>

<style>
  .calc-item {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-rows: 1fr;
    grid-template-areas: "a b c d";
    grid-column-gap: 1rem;

    padding: 30px 20px;
    border-radius: 4px;
    background-color: #c5d2d5;
    margin-bottom: 20px;
  }

  fieldset {
    margin: 0;
  }

  fieldset div {
    display: flex;
    align-items: center;
    height: 30px;
    margin-bottom: 20px;
  }

  fieldset div label {
    margin-left: 15px;
  }

  /* button[disabled] {
    background-color: currentColor;
    color: #c0c0c0;
  } */

  .total {
    margin-bottom: 20px;
  }

  .calc-item-field {
    min-width: 200px;
  }

  .calc-item-field:first-child {
    grid-area: a;
  }

  .calc-item-field:nth-child(2) {
    grid-area: b;
  }

  .calc-item-field:nth-child(3) {
    grid-area: c;
  }

  .calc-item-field:nth-child(4) {
    grid-area: d;
  }

  @media (max-width: 1100px) {
    .calc-item {
      grid-template-columns: 50% 50%;
      grid-template-rows: 80px 1fr 200px;
      grid-template-areas:  "a a"
                            "b c"
                            "d d";
    }

    .calc-item-field {
      margin-bottom: 1rem;
    }
  }

  @media (max-width: 600px) {
    .calc-item {
      grid-template-columns: 50% 50%;
      grid-template-rows: 100px 1fr 1fr 200px;
      grid-template-areas:  "a a"
                            "b b"
                            "c c"
                            "d d";
    }
  }
</style>