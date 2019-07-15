<script>
  const max = 99;
  const url = `https://pokeapi.co/api/v2/pokemon/?limit=${max}`;
  const getPokemons = async () => {
    let response = await fetch(url);
    let data = await response.json();
    // https://flaviocopes.com/javascript-async-await-array-map/*/
    let getSprites = async () =>
      await Promise.all(
        data.results.map(async pokemon => {
          let response = await fetch(pokemon.url);
          let data = await response.json();
          return {
            name: pokemon.name,
            types: data.types,
            spriteUrl: data.sprites.front_default
          };
        })
      );
    return await getSprites();
  };

  let pokemons = getPokemons();
</script>

<style src="./style.styl">

</style>

<ul>
  {#await pokemons}
    <p>...waiting</p>
  {:then pokemons}
    {#each pokemons as pokemon}
      <li class="cell">
        <img src={pokemon.spriteUrl} alt={pokemon.name} />
        <h4>{pokemon.name}</h4>
        <ul>
          {#each pokemon.types as type}
            <li>{type.type.name}</li>
          {/each}
          <ul />
        </ul>
      </li>
    {/each}
  {:catch error}
    <p style="color: red">{error.message}</p>
  {/await}
</ul>
