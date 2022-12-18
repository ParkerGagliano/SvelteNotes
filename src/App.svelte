<script>
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
  import Register from "./lib/Register.svelte";
  import Home from "./lib/Home.svelte";
  import Login from "./lib/Login.svelte";
  let authenticated = false;
  let username = "";
  let token;
  function successfulLogin(result) {
    authenticated = true;
    token = "JWT " + result.accessToken;
    console.log(token);
    username = result.user.username;
  }
</script>

<main>
  {#if authenticated}
    <Home {authenticated} {username} {token} />
  {:else}
    <div class="container">
      <div class="row justify-content-center">
        <div class="col"><Register /></div>
      </div>
      <div class="row mt-5">
        <div class="col"><Login {successfulLogin} /></div>
      </div>
    </div>
  {/if}
</main>
