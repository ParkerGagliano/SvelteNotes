<script>
  import { fade } from "svelte/transition";
  export let token;
  export let username;
  export let authenicated;
  let secret;
  function getSecretData() {
    fetch("http://localhost:3000/api/login", {
      method: "GET",
      headers: {
        Authorization: token,
      },
    })
      .then((response) => response.text())
      .then((data) => {
        console.log(data);
        handleData(data);
      });
  }
  function handleData(data) {
    if (data.includes("jwt expired") == true) {
      authenicated = false;
      return;
    }
    console.log(data);
    secret = data;
  }
</script>

<div transition:fade class="container">
  <div class="row">
    <div class="col">
      <h3>Welcome back: {username}</h3>
    </div>
  </div>
  <div class="row">
    <div class="col">
      {#if secret}
        <h1 transition:fade>{secret}</h1>
      {/if}
    </div>
  </div>
  <div class="row">
    <div class="col">
      <button on:click={getSecretData}>Reveal only logged in info</button>
    </div>
  </div>
</div>
