<!-- App.svelte -->
<script>
  import { onMount } from 'svelte';

  let cocktails = [];
  let selectedCocktail = null;
  let searchInput = '';

  const fetchCocktailsByName = async (name) => {
    const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/search.php?s=${name}`);
    const data = await response.json();
    cocktails = data.drinks || []; // Asignar un array vacío si no hay cócteles encontrados
    selectedCocktail = null; // Reset selected cocktail when fetching new ones
  };

  const fetchCocktailById = async (id) => {
    const response = await fetch(`https://www.thecocktaildb.com/api/json/v1/1/lookup.php?i=${id}`);
    const data = await response.json();
    selectedCocktail = data.drinks ? data.drinks[0] : null; // Asignar null si no se encuentra ningún cóctel
  };

  onMount(() => {
    // Cargar algunos cócteles al inicio
    fetchCocktailsByName('margarita');
  });

  const handleSearch = () => {
    if (searchInput.trim() !== '') {
      fetchCocktailsByName(searchInput);
    }
  };

  const handleCocktailClick = (id) => {
    fetchCocktailById(id);
  };
</script>

<h1>Explora Cócteles</h1>

<input type="text" bind:value={searchInput} placeholder="Buscar cóctel por nombre">
<button on:click={handleSearch}>Buscar</button>

<div class="cocktail-list">
  {#each cocktails as cocktail}
    
      <div class="cocktail" on:click={() => handleCocktailClick(cocktail.idDrink)}>
        <img src={cocktail.strDrinkThumb} alt={cocktail.strDrink}>
        <p>{cocktail.strDrink}</p>
      </div>
    

  {/each}
</div>

{#if selectedCocktail}
  <div class="cocktail-details">
    <h2>{selectedCocktail.strDrink}</h2>
    <img src={selectedCocktail.strDrinkThumb} alt={selectedCocktail.strDrink}>
    <p>{selectedCocktail.strInstructions}</p>
    <h3>Ingredientes:</h3>
    <ul>
      {#each Array.from({ length: 15 }, (_, i) => i + 1) as index}
        {#if selectedCocktail[`strIngredient${index}`]}
          <li>{selectedCocktail[`strIngredient${index}`]} - {selectedCocktail[`strMeasure${index}`]}</li>
        {/if}
      {/each}
    </ul>
  </div>
{/if}

<style>
  h1 {
    text-align: center;
  }

  input, button {
    margin: 10px;
  }

  .cocktail-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .cocktail {
    margin: 10px;
    cursor: pointer;
    text-align: center;
  }

  .cocktail img {
    width: 150px;
    height: 150px;
    object-fit: cover;
    border-radius: 5px;
  }

  .cocktail-details {
    margin-top: 20px;
    text-align: center;
  }

  .cocktail-details img {
    width: 200px;
    height: 200px;
    object-fit: cover;
    border-radius: 5px;
    margin-bottom: 10px;
  }

  ul {
    list-style: none;
    padding: 0;
  }

  li {
    margin: 5px 0;
  }
</style>
