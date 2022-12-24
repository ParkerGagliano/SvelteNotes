<script>
  import { slide } from "svelte/transition";

  let message = "";
  let tone = "";
  let password = "";
  let username = "";
  export let successfulLogin;
  console.log();

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

    return true;
  }

  function handleError(a) {
    message = a;
    tone = "danger";
    setTimeout(() => {
      message = "";
    }, 1500);
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
        .catch((error) => handleError(error));
    }
  }

  function handleResult(result) {
    console.log(result);
    if (result.message == "Login successfull") {
      console.log("success");
      successfulLogin(result);
    } else {
      message = result.message;
    }
  }

  // Set the JWT as a cookie that expires in 7 days
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

        <div class="form-group mt-2">
          <button
            class="form-control"
            id="confirmPassword"
            on:click={handleLogin}
          >
            Login</button
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
