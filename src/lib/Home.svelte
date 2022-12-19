<script>
  import { fade } from "svelte/transition";
  export let token;
  export let username;
  export let authenicated;
  let secret = "";
  function getSecretData() {
    fetch("http://localhost:3000/api/login", {
      method: "GET",
      headers: {
        Authorization: token,
      },
    })
      .then((response) => response.text())
      .then((data) => {
        handleData(data);
      });
  }
  function handleData(data) {
    if (data.includes("jwt expired") == true) {
      authenicated = false;
      return;
    }
    secret = data;
  }
</script>

<div transition:fade class="container">
  <div class="row">
    <div class="col">
      <h3 class="text-center">{username.toUpperCase()}'s Notes</h3>
    </div>
  </div>
</div>
