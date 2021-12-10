<script lang="ts">
  import pokemon from "./pokemon";
  import { _types, typeTable, タイプ, 相性 } from "./type";

  let types = _types;

  let searchPokemonQuery = "";
  let selectedPokemon;
  let selectedType1;
  let selectedType2;

  const t = {};

  Object.entries(typeTable).forEach(([type, table]) => {
    Object.entries(table).forEach((r) => {
      if (!t[r[0]]) {
        t[r[0]] = {};
      }
      t[r[0]][type] = r[1];
    });
  });

  Object.entries(t).forEach(([type, table]) => {
    console.log(
      type,
      Object.entries(table).filter((r) => {
        return r[1] === "◯";
      })
    );
  });

  function hiraToKana(str) {
    return str.replace(/[\u3041-\u3096]/g, function (match) {
      var chr = match.charCodeAt(0) + 0x60;
      return String.fromCharCode(chr);
    });
  }

  function selectPokemon(pokemon) {
    selectedType1 = undefined;
    selectedType2 = undefined;
    selectedPokemon = pokemon;
    searchPokemonQuery = "";
  }

  function selectType(type) {
    selectedPokemon = undefined;
    if (!selectedType1 && !selectedType2) {
      selectedType1 = type;
    } else if (selectedType1 && selectedType2) {
      selectedType1 = type;
      selectedType2 = undefined;
    } else if (selectedType1) {
      selectedType2 = type;
    }
  }

  $: filterdPokemon = pokemon.filter(
    (p) =>
      searchPokemonQuery !== "" &&
      (p.name.includes(searchPokemonQuery) ||
        p.name.includes(hiraToKana(searchPokemonQuery)))
  );

  $: _selectedType1 = selectedPokemon ? selectedPokemon.type[0] : selectedType1;
  $: _selectedType2 = selectedPokemon ? selectedPokemon.type[1] : selectedType2;

  function typeMagnification(i: 相性) {
    switch (i) {
      case " ":
        return 1;
      case "×":
        return 0;
      case "△":
        return 0.5;
      case "◯":
        return 2;
      case undefined: {
        return 0;
      }
    }
  }

  function calcMagnification(
    selectedType1: タイプ,
    selectedType2: タイプ,
    type: タイプ
  ) {
    let type1Magnification = selectedType1
      ? typeMagnification(typeTable[selectedType1][type])
      : 1;
    let type2Magnification = selectedType2
      ? typeMagnification(typeTable[selectedType2][type])
      : 1;

    return type1Magnification * type2Magnification;
  }
</script>

<div class="container">
  <table class="table is-bordered is-fullwidth">
    <tr
      ><td colspan="4"
        ><input class="input" type="text" bind:value={searchPokemonQuery} /></td
      ></tr
    >
    {#each filterdPokemon as p}
      <tr>
        <td on:click={() => selectPokemon(p)}>{p.name}</td>
      </tr>
    {/each}
    <tr>
      <td colspan="4">
        <div class="tags">
          {#each types as type}
            <span
              class="tag"
              on:click={() => selectType(type)}
              class:is-select={_selectedType1 === type ||
                _selectedType2 === type}>{type}</span
            >
          {/each}
        </div>
      </td>
    </tr>
  </table>

  <table class="table is-bordered is-fullwidth">
    <tr>
      <th>選択ポケモン</th>
      <td>{selectedPokemon ? selectedPokemon.name : ""}</td>
    </tr>
    <tr>
      <th>選択タイプ1</th>
      <td>{_selectedType1 ? _selectedType1 : ""}</td>
    </tr>
    <tr>
      <th>選択タイプ2</th>
      <td>{_selectedType2 ? _selectedType2 : ""}</td>
    </tr>
  </table>

  <table style="width: 100%;" class="table is-bordered is-fullwidth">
    <tr>
      <th style="width: 25%;" />
      <td style="width: 25%;" />
      <th style="width: 25%;">{_selectedType1 ? _selectedType1 : " "}</th>
      <th style="width: 25%;">{_selectedType2 ? _selectedType2 : " "}</th>
    </tr>
    {#each types as type}
      <tr>
        <th class={`type-${type}`}>{type}</th>
        <th
          class={`mag-${calcMagnification(_selectedType1, _selectedType2, type)
            .toString()
            .replace(".", "")}`}
          >{calcMagnification(_selectedType1, _selectedType2, type)}</th
        >

        <th>
          {#if _selectedType1}
            {typeTable[_selectedType1][type]}
          {/if}
        </th>
        <th>
          {#if _selectedType2}
            {typeTable[_selectedType2][type]}
          {/if}
        </th>
      </tr>
    {/each}
  </table>
</div>

<style>
  .tag.is-select {
    background-color: green;
    color: white;
  }

  .mag-4 {
    background-color: green;
    color: white;
  }

  .mag-2 {
    background-color: lightgreen;
    color: white;
  }

  .mag-1 {
  }

  .mag-05 {
    background-color: yellow;
    color: black;
  }

  .mag-0 {
    background-color: red;
    color: wheat;
  }

  .type-ノーマル {
    background-color: #909ba2;
    color: white;
  }

  .type-ほのお {
    background-color: #ec9854;
    color: white;
  }

  .type-みず {
    background-color: #4d8dd6;
    color: white;
  }

  .type-でんき {
    background-color: #f3d62d;
    color: white;
  }

  .type-くさ {
    background-color: #62b955;
    color: white;
  }

  .type-こおり {
    background-color: #77ccbe;
    color: white;
  }

  .type-かくとう {
    background-color: #c54668;
    color: white;
  }

  .type-どく {
    background-color: #ac69ce;
    color: white;
  }

  .type-じめん {
    background-color: #df773d;
    color: white;
  }

  .type-ひこう {
    background-color: #8facd5;
    color: white;
  }

  .type-エスパー {
    background-color: #f9717e;
    color: white;
  }

  .type-むし {
    background-color: #91c02f;
    color: white;
  }

  .type-いわ {
    background-color: #c7b68b;
    color: white;
  }

  .type-ゴースト {
    background-color: #4f6ab0;
    color: white;
  }

  .type-ドラゴン {
    background-color: #1069bd;
    color: white;
  }

  .type-あく {
    background-color: #585563;
    color: white;
  }

  .type-はがね {
    background-color: #5d8ca6;
    color: white;
  }

  .type-フェアリー {
    background-color: #e692e1;
    color: white;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
