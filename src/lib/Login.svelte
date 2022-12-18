<script>
  export let successfulLogin;
  import { fade } from "svelte/transition";
  let modal = false;
  let message = "joe";
  let password = "";
  let username = "";

  function checkValid() {
    if (username == "") {
      modal = true;
      message = "Username cannot be empty";
      return false;
    }
    if (username.length < 3) {
      modal = true;
      message = "Username must be at least 3 characters";
      return false;
    }
    if (password == "") {
      modal = true;
      message = "Password cannot be empty";
      return false;
    }
    if (password.length < 8) {
      modal = true;
      message = "Password must be at least 8 characters";
      return false;
    }
    return true;
  }

  function handleLogin() {
    let response = checkValid();
    if (response == true) {
      let myHeaders = new Headers();
      let urlencoded = new URLSearchParams();
      urlencoded.append("username", username);
      urlencoded.append("password", password);
      myHeaders.append("Content-Type", "application/x-www-form-urlencoded");
      let requestOptions = {
        method: "POST",
        headers: myHeaders,
        body: urlencoded,
      };

      fetch(`http://127.0.0.1:3000/api/login`, requestOptions)
        .then((response) => response.json())
        .then((result) => handleResult(result))
        .catch((error) => alert(error));
    }
  }

  function handleResult(result) {
    console.log(result);
    if (result.message == "Login successfull") {
      console.log("success");
      successfulLogin(result);
    } else {
      modal = true;
      message = result.message;
    }
  }
</script>

<div class="row">
  <div class="col">
    <div class="row">
      <div class="col">
        <h3>Login</h3>
      </div>
    </div>
    <div class="row">
      <form action="#">
        <div class="form-group">
          <input
            type="username"
            class="form-control"
            id="username"
            placeholder="Username"
            bind:value={username}
          />
        </div>
        <div class="form-group mt-2">
          <input
            type="password"
            class="form-control"
            id="password"
            placeholder="Password"
            bind:value={password}
          />
        </div>
        {#if modal}
          <div
            transition:fade={{ duration: 100 }}
            class="bg-danger mt-2 rounded-pill"
          >
            {message}
          </div>
        {/if}
        <div class="form-group mt-2">
          <button
            class="form-control"
            id="confirmPassword"
            on:click={handleLogin}
          >
            Login</button
          >
        </div>
      </form>
    </div>
  </div>
</div>
