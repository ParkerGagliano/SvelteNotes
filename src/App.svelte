<script>
  import svelteLogo from "./assets/svelte.svg";
  import Counter from "./lib/Counter.svelte";
  import Register from "./lib/Register.svelte";
  import Home from "./lib/Home.svelte";
  import Login from "./lib/Login.svelte";
  import AddDoc from "./lib/AddDoc.svelte";
  import { fade } from "svelte/transition";
  import { fly } from "svelte/transition";
  let registerpage = true;
  let authenticated = false;
  let username = "";
  let user_id = "";
  let token;
  let loginpage = false;
  let buttonEnabled = true;
  let message = "";
  let tone = "";

  $: console.log(loginpage);

  function successfulLogin(result) {
    loginpage = false;
    registerpage = false;
    buttonEnabled = false;
    setTimeout(() => {
      authenticated = true;
      token = "JWT " + result.accessToken + " " + result.user.id;
      console.log(token);
      username = result.user.username;
      user_id = result.user.id;
    }, 600);
  }
  function toggleRegister() {
    if (registerpage == true) {
      registerpage = false;
      buttonEnabled = false;
      setTimeout(() => {
        loginpage = true;
        buttonEnabled = true;
      }, 500);
    } else {
      loginpage = false;
      buttonEnabled = false;
      setTimeout(() => {
        registerpage = true;
        buttonEnabled = true;
      }, 500);
    }
  }

  function handleSuccessfulRegister() {
    toggleRegister();
    handleMessage("Registration Successful");
  }

  function handleError(a) {
    message = a;
    tone = "danger";
    setTimeout(() => {
      message = "";
    }, 1500);
  }

  function handleMessage(a) {
    message = a;
    tone = "success";
    setTimeout(() => {
      message = "";
    }, 1500);
  }
</script>

<main class="container">
  {#if message != ""}
    <div
      transition:fly={{ y: -200, duration: 500 }}
      style="position: absolute; margin-left: 27vw; top: 0; min-width: 10vw;"
      class="row justify-content-center bg-{tone} mt-1 rounded-pill"
    >
      <div class="col-auto">
        <h4 class="m-0 p-0">{message}</h4>
      </div>
    </div>
  {/if}
  {#if authenticated}
    <Home {authenticated} {username} {token} />
    <AddDoc {user_id} {token} />
  {:else}
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-auto mt-5">
          {#if buttonEnabled == true}
            <button
              class="btn btn-info"
              transition:fly={{ y: -200, duration: 500 }}
              on:click={toggleRegister}
              >{#if registerpage == true}Login{:else}Register{/if}</button
            >
          {/if}
        </div>
      </div>
      {#if registerpage == true}
        <div
          transition:fly={{ x: -200, duration: 500 }}
          class="row justify-content-center"
        >
          <div style="margin-top: 25vh" class="col">
            <Register {handleSuccessfulRegister} />
          </div>
        </div>
      {/if}
      {#if loginpage == true}
        <div transition:fly={{ x: -200, duration: 500 }} class="row  mt-5">
          <div style="margin-top: 25vh" class="col">
            <Login {successfulLogin} />
          </div>
        </div>
      {/if}
    </div>
  {/if}
</main>
