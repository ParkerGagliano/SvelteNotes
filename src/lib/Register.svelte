<script>
  import { slide } from "svelte/transition";

  let password = "";
  let confirmPassword = "";
  let username = "";
  let message = "";
  let tone = "";
  export let handleSuccessfulRegister;
  function checkValid() {
    if (username == "") {
      handleError("Username cannot be empty");

      return false;
    }
    if (username.length < 3) {
      handleError("Username must be at least 3 characters");
      return false;
    }
    if (password == "") {
      handleError("Password cannot be empty");
      return false;
    }
    if (password.length < 8) {
      handleError("Password must be at least 8 characters");
      return false;
    }
    if (password == confirmPassword) {
      return true;
    } else {
      handleError("Passwords do not match");
      return false;
    }
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

  function handleRegister() {
    let response = checkValid();
    if (response == true) {
      console.log("dasdasdasds");
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

      fetch(`http://127.0.0.1:3000/api/register`, requestOptions)
        .then((response) => response.json())
        .then((result) => handleSuccessfulRegister())
        .catch((error) => handleError(error));
    }
  }
</script>

<div class="row">
  <div class="col">
    <div class="row">
      <div class="col">
        <h3>Register</h3>
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
        <div class="form-group mt-2">
          <input
            type="password"
            class="form-control"
            id="confirmPassword"
            placeholder="Confirm Password"
            bind:value={confirmPassword}
          />
        </div>

        <div class="form-group mt-2">
          <button
            class="form-control"
            id="confirmPassword"
            on:click={handleRegister}
          >
            Register</button
          >
        </div>
        {#if message}
          <div
            transition:slide={{ duration: 200 }}
            class="bg-danger mt-2 rounded-pill"
          >
            <p class="text-center">{message}</p>
          </div>
        {/if}
      </form>
    </div>
  </div>
</div>
