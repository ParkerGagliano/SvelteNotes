<script>
  export let token;
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
    console.log(data);
    secret = data;
  }
</script>

<div class="container">
  <div class="row">
    <div class="col">
      <h3>Register</h3>
    </div>
  </div>
  <div class="row">
    <div class="col">
      <h1>{secret}</h1>
    </div>
  </div>
  <div class="row">
    <div class="col">
      <button on:click={getSecretData} />
    </div>
  </div>
</div>
