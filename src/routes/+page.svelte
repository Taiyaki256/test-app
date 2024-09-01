<script lang="ts">
  import { invoke } from "@tauri-apps/api/tauri";
  import { appWindow } from "@tauri-apps/api/window";
  import { AppBar } from "@skeletonlabs/skeleton";
  import { onMount } from "svelte";
  let name = "";
  let greetMsg = "";
  let isFullscreen = false;

  async function fullscreen() {
    isFullscreen = await appWindow.isFullscreen();
    if (!isFullscreen) {
      appWindow.setFullscreen(true);
    } else {
      appWindow.setFullscreen(false);
    }
    isFullscreen = !isFullscreen;
  }
  function minimize() {
    appWindow.minimize();
  }

  async function maximize() {
    let maximizeState = await appWindow.isMaximized();

    if (!maximizeState) {
      appWindow.maximize();
    } else {
      appWindow.unmaximize();
    }
  }

  function hide() {
    appWindow.hide();
  }

  async function greet() {
    // Learn more about Tauri commands at https://tauri.app/v1/guides/features/command
    greetMsg = await invoke("greet", { name });
  }

  onMount(() => {
    const appBar = document.querySelectorAll(
      ".app-bar-row-main, .app-bar-slot-lead, .app-bar-slot-trail, .app-bar-slot-default"
    );
    for (let i = 0; i < appBar.length; i++) {
      appBar[i].setAttribute("data-tauri-drag-region", "");
    }
  });
</script>

<AppBar class="pt-1 pl-1 pr-1 pb-0 select-none {isFullscreen ? 'hidden' : ''}">
  <!-- <div data-tauri-drag-region class="select-none"> -->
  <svelte:fragment slot="lead">(icon)</svelte:fragment>
  Test-App
  <svelte:fragment slot="trail">
    <button
      class="h-8 pt-1 pb-1"
      id="titlebar-fullscreen"
      on:click={fullscreen}
    >
      <img
        src="https://api.iconify.design/mdi:window-minimize.svg"
        alt="fullscreen"
        class="h-full"
      />
    </button>
    <button class="h-8 pt-1 pb-1" id="titlebar-minimize" on:click={minimize}>
      <img
        src="https://api.iconify.design/mdi:window-minimize.svg"
        alt="minimize"
        class="h-full"
      />
    </button>
    <button class="h-8 pt-1 pb-1" id="titlebar-maximize" on:click={maximize}>
      <img
        src="https://api.iconify.design/mdi:window-maximize.svg"
        alt="maximize"
        class="h-full"
      />
    </button>
    <button class="h-8 pt-1 pb-1" id="titlebar-close" on:click={hide}>
      <img
        src="https://api.iconify.design/mdi:close.svg"
        alt="close"
        class="h-full"
      />
    </button>
  </svelte:fragment>
</AppBar>
<!-- <div data-tauri-drag-region class="titlebar">
  <button class="titlebar-button" id="titlebar-minimize" on:click={minimize}>
    <img
      src="https://api.iconify.design/mdi:window-minimize.svg"
      alt="minimize"
    />
  </button>
  <div class="titlebar-button" id="titlebar-maximize">
    <img
      src="https://api.iconify.design/mdi:window-maximize.svg"
      alt="maximize"
    />
  </div>
  <div class="titlebar-button" id="titlebar-close">
    <img src="https://api.iconify.design/mdi:close.svg" alt="close" />
  </div>
</div> -->
<div class="container">
  <h1>Welcome to Tauri!</h1>

  <div class="row">
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo vite" alt="Vite Logo" />
    </a>
    <a href="https://tauri.app" target="_blank">
      <img src="/tauri.svg" class="logo tauri" alt="Tauri Logo" />
    </a>
    <a href="https://kit.svelte.dev" target="_blank">
      <img src="/svelte.svg" class="logo svelte-kit" alt="SvelteKit Logo" />
    </a>
  </div>

  <p>Click on the Tauri, Vite, and SvelteKit logos to learn more.</p>

  <form class="row" on:submit|preventDefault={greet}>
    <input id="greet-input" placeholder="Enter a name..." bind:value={name} />
    <button type="submit">Greet</button>
  </form>

  <p>{greetMsg}</p>
</div>

<style>
  .logo.vite:hover {
    filter: drop-shadow(0 0 2em #747bff);
  }

  .logo.svelte-kit:hover {
    filter: drop-shadow(0 0 2em #ff3e00);
  }

  .titlebar {
    height: 30px;
    background: #329ea3;
    user-select: none;
    display: flex;
    justify-content: flex-end;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
  }
  .titlebar-button {
    display: inline-flex;
    justify-content: center;
    align-items: center;
    width: 30px;
    height: 30px;
    background: #329ea3;
    border: none;
  }
  .titlebar-button:hover {
    background: #5bbec3;
  }

  :root {
    font-family: Inter, Avenir, Helvetica, Arial, sans-serif;
    font-size: 16px;
    line-height: 24px;
    font-weight: 400;

    color: #0f0f0f;
    background-color: transparent;

    font-synthesis: none;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    -webkit-text-size-adjust: 100%;
    height: 100%;
    width: 100%;
    border-radius: 10px;
    overflow: hidden;
  }

  .container {
    margin: 0;
    padding-top: 10vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
  }

  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: 0.75s;
  }

  .logo.tauri:hover {
    filter: drop-shadow(0 0 2em #24c8db);
  }

  .row {
    display: flex;
    justify-content: center;
  }

  a {
    font-weight: 500;
    color: #646cff;
    text-decoration: inherit;
  }

  a:hover {
    color: #535bf2;
  }

  h1 {
    text-align: center;
  }

  form input,
  form button {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    color: #0f0f0f;
    background-color: #ffffff;
    transition: border-color 0.25s;
    box-shadow: 0 2px 2px rgba(0, 0, 0, 0.2);
  }

  form button {
    cursor: pointer;
  }

  form button:hover {
    border-color: #396cd8;
  }
  form button:active {
    border-color: #396cd8;
    background-color: #e8e8e8;
  }

  form input,
  form button {
    outline: none;
  }

  #greet-input {
    margin-right: 5px;
  }

  @media (prefers-color-scheme: dark) {
    :root {
      color: #f6f6f6;
      background-color: #2f2f2f;
    }

    a:hover {
      color: #24c8db;
    }

    form input,
    form button {
      color: #ffffff;
      background-color: #0f0f0f98;
    }
    form button:active {
      background-color: #0f0f0f69;
    }
  }
</style>
