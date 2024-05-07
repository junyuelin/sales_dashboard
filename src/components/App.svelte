<script>
  let showDropdown = false;
  const products = [
    "Men's Street Footwear",
    "Men's Athletic Footwear",
    "Women's Street Footwear",
    "Women's Athletic Footwear",
    "Men's Apparel",
    "Women's Apparel"
  ];

  function toggleDropdown() {
    showDropdown = !showDropdown;
  }

  function selectProduct(product) {
    console.log("Selected product:", product);
    // You can perform any action with the selected product here
  }
</script>

<main>
  <h1>Sales Dashboard</h1>

  <div class="dropdown" on:click={toggleDropdown}>
    <div class="selected-product">Select a product</div>
    {#if showDropdown}
      <div class="options">
        {#each products as product}
          <div class="option" on:click={() => selectProduct(product)}>{product}</div>
        {/each}
      </div>
    {/if}
  </div>
</main>

<style>
  .dropdown {
    position: relative;
    width: 200px;
    border: 1px solid #ccc;
    border-radius: 5px;
    cursor: pointer;
  }

  .selected-product {
    padding: 10px;
  }

  .options {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    background-color: #fff;
    border: 1px solid #ccc;
    border-top: none;
    border-radius: 0 0 5px 5px;
    z-index: 1;
  }

  .option {
    padding: 10px;
    border-bottom: 1px solid #ccc;
  }

  .option:hover {
    background-color: #f0f0f0;
  }
</style>
