<script>
  const docSide = document.getElementById("addTitles");
  export let token;
  export let user_id;
  let content = "";
  let title = "";
  let alldocs = [];
  let currentdoc = null;

  function updateCurrentDoc(i) {
    content = alldocs[i].content;
    title = alldocs[i].title;
  }

  function getCurrentUserDocs() {
    let myHeaders = new Headers();
    myHeaders.append("Authorization", token);
    let URLSearchParams = new URLSearchParams();
    URLSearchParams.append("user_id", user_id);
    let requestOptions = {
      method: "GET",
      headers: myHeaders,
    };
    fetch(`http://127.0.0.1:3000/api/docs/owner`, requestOptions)
      .then((response) => response.json())
      .then((result) => fillSideBar(result))
      .catch((error) => console.log("error", error));
  }

  fetch(`http://127.0.0.1:3000/api/docs`)
    .then((response) => response.json())
    .then((result) => handleDoc(result))
    .catch((error) => console.log("error", error));

  function handleDoc(data) {
    alldocs = [alldocs, ...data];
  }

  function getbyOwnerID() {
    let myHeaders = new Headers();
    myHeaders.append("Authorization", token);
    let requestOptions = {
      method: "GET",
      headers: myHeaders,
    };
    fetch(`http://127.0.0.1:3000/api/docs/owner`, requestOptions)
      .then((response) => response.text())
      .then((result) => console.log(result))
      .catch((error) => console.log("error", error));
  }
  function fillSideBar(data) {
    if (data != null) {
      for (let i = 0; i < data.length; i++) {
        let title = data[i].title;
        let id = data[i].id;
        let newDiv = document.createElement("div");
        newDiv.innerHTML = `<button class="btn btn-primary" onclick="loadDoc(${data[i]})">${title}</button>`;
        docSide.appendChild(newDiv);
      }
    }
  }

  function handleSaveForm() {
    let myHeaders = new Headers();
    let urlencoded = new URLSearchParams();
    urlencoded.append("title", title);
    urlencoded.append("content", content);
    urlencoded.append("owner_id", user_id);
    myHeaders.append("Content-Type", "application/x-www-form-urlencoded");
    let requestOptions = {
      method: "POST",
      headers: myHeaders,
      body: urlencoded,
    };

    fetch(`http://127.0.0.1:3000/api/docs`, requestOptions)
      .then((response) => response.text())
      .then((result) => alert(result))
      .catch((error) => alert(error));
  }

  function handleEditForm() {
    let filename = document.getElementById("title").value;
    let myHeaders = new Headers();
    let requestOptions = {
      method: "PATCH",
      headers: myHeaders,
    };

    fetch(`http://127.0.0.1:3000/api/docs`, requestOptions);
  }
</script>

<div class="container-fluid">
  <div class="row">
    <div class="col-2 bg-secondary" style="height: 100vh">
      <div class="row justify-content-center" id="addTitles">
        {#each alldocs as doc, i}
          <button
            class="btn mt-2 btn-primary col-10 rounded-pill"
            on:click={() => updateCurrentDoc(i)}
          >
            {doc.title}
          </button>
        {/each}
      </div>
    </div>
    <form class="col-10" style="height: 100vh">
      <div class="row justify-content-around mt-1">
        <div class="col-2">
          <button class="btn-success btn" on:click={handleSaveForm}>save</button
          >
        </div>
        <div class="col-3">
          <input
            type="text"
            bind:value={title}
            id="title"
            placeholder="Title"
            class="form-control"
          />
        </div>

        <div class="col-2">
          <button class="btn-info btn" id="new-file">new file</button>
        </div>
      </div>
      <div class="row" style="height: 100%; width: 100%">
        <div class="col">
          <textarea
            bind:value={content}
            class="mt-2"
            id="main-text"
            type="text"
            style="height: 100%; width: 100%"
          />
        </div>
      </div>
    </form>
    <div class="col">
      <button class="btn btn-primary" on:click={getbyOwnerID}
        >getbyOwnerID</button
      >
    </div>
  </div>
</div>
