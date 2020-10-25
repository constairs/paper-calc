<script>
  import { onMount } from 'svelte';
  import Calc from './Calc.svelte';
  import * as animateScroll from 'svelte-scrollto';
  import { v4 as uuidv4 } from 'uuid';

  animateScroll.setGlobalOptions({
    container: 'main',
    offset: 466,
  })

  const initItem = () => ({
    id: uuidv4(),
    listsCount: '',
    itemDensity: "220",
    itemSize: `${70 * 100}`,
  });

  const handleAddItem = () => {
    calcItems = calcItems.concat(initItem());

    console.log(calcItems);
    animateScroll.scrollToBottom();
  };

  const addToFavorite = (item) => {
    console.log(item);
    if (!history.find(a => (a.id === item.id))) {
      history = history.concat(item);
      localStorage.setItem('history', JSON.stringify(history));
    } else {
      history = history.map(a => (a.id === item.id ? item : a));
    }
  };

  const removeFromFavourite = (id) => {
    console.log(id);
    history = history.filter(a => (a.id !== id));
    console.log(history);

    localStorage.setItem('history', JSON.stringify(history));
  };

  const updateItem = (updated) => {
    console.log(updated);
    calcItems = calcItems.map((a) => (a.id === updated.id
      ? ({ ...a, ...updated })
      : (a))
    );
  };

  const openSidebar = () => {
    sidebarIsOpened = !sidebarIsOpened;
  };

  let sidebarIsOpened = false;

  export let calcItems = [];

  export let history = [];

  function getHistory() {
    history = JSON.parse(localStorage.getItem('history')) || [];
  }

  onMount(() => {
    getHistory();
    calcItems = calcItems.concat(initItem())
  });
</script>

<div class="main-container">
  <main class="main-block">
    <div class="main-block-content">
      <div class="main-heading">
        <div class="img">
          <img src="./main-image.png" alt="Стопка бумаги изображение" />
        </div>
        <h1>Paper tons calculator</h1>
      </div>
      {#each calcItems as item}
        <Calc
          bind:item={item}
          itemDensity={item.itemDensity}
          addToFavorite={addToFavorite}
          updateItem={updateItem}
        />
      {/each}
    
      <button class="add-button" on:click={handleAddItem}>+ Добавить ещё</button>
    </div>
  </main>
  
  <aside class={sidebarIsOpened ? 'opened' : ''}>
    <button class="switch-btn" on:click={openSidebar}>
      {#if !sidebarIsOpened}
        open
      {:else}
        close
      {/if}
    </button>
    <h3>Избранное</h3>
    {#if history.length}
      <ul class="history-list">
        {#each history as histItem}
          <li>
            <span>{histItem.listsCount} x</span>
              <span>
                {histItem.itemSizeLabel} мм
              </span>
              <span>
                {histItem.itemDensity} кг/м2
              </span>
            <button
              on:click={() => {
                navigator.clipboard.writeText(`${histItem.totalTons}`)
                .then()
                .catch();  
              }}
            >
              {histItem.totalTons} т.
            </button>
            <button
              on:click={() => {
                removeFromFavourite(histItem.id);
              }}
            >
              x
            </button>
          </li>
        {/each}
      </ul>
    {/if}
  </aside>
</div>


<style>
  .main-container {
    height: 100%;
    max-height: 100vh;
    display: flex;
  }

  .main-block-content {
    max-width: 1000px;
  }

	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
    overflow-y: auto;
    flex: 1 1 0;
	}

  aside {
    overflow-y: auto;
    overflow-x: hidden;
    padding-top: 30px;
    padding-left: 20px;
    padding-right: 20px;
    border-left: 3px solid #c0c0c0;
    flex: 0 0 500px;
  }

  .switch-btn {
    position: absolute;
    top: 0;
    left: 0;
    display: none;
  }

  .main-heading {
    display: flex;
  }

  .main-heading .img {
    width: 200px;
    height: 200px;
  }

  .main-heading .img img {
    max-width: 100%;
    display: block;
    height: auto;
  }

  .add-button {
    position: fixed;
    left: 50%;
    bottom: 30px;
    transform: translateX(-50%);
  }

	h1 {
		color: #c0c0c0;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

  .history-list {
    list-style-type: none;
    padding-left: 0;
  }

  .history-list li {
    display: flex;
    margin-bottom: 15px;
    padding-bottom: 5px;
    border-bottom: 1px solid #c0c0c0;
  }

  .history-list li span {
    margin-right: 10px;
  }

  @media (max-width: 1600px) {
    aside {
      position: fixed;
      right: 0;
      top: 0;
      max-width: 500px;
      width: 100%;
      height: 100vh;
      background-color: #f0f0f0;
      transform: translateX(500px);
      transition: all .2s ease-in-out;
    }

    aside.opened {
      transform: translateX(0);
    }

    .switch-btn {
      display: block;
    }
  }

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>