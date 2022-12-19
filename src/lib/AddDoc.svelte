<script>
  import { get } from "svelte/store";
  import { fade } from "svelte/transition";
  import { fly } from "svelte/transition";

  const docSide = document.getElementById("addTitles");
  export let token;
  export let user_id;
  let content = "";
  let title = "";
  let alldocs = [0];
  let currentdoc = null;

  function updateCurrentDoc(i) {
    content = alldocs[i].content;
    title = alldocs[i].title;
  }

  function getCurrentUserDocs() {
    let myHeaders = new Headers();
    myHeaders.append("Authorization", token);
    let urlencoded = new URLSearchParams();
    let requestOptions = {
      method: "GET",
      headers: myHeaders,
    };
    fetch(`http://127.0.0.1:3000/api/docs/owner`, requestOptions)
      .then((response) => response.json())
      .then((result) => temp(result))
      .catch((error) => console.log("error", error));
  }

  function temp(data) {
    if (alldocs[0] == 0) {
      alldocs = data;
    } else {
      alldocs = [...alldocs, data];
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
      .then((response) => response.json())
      .then((result) => temp(result)) //need more here to veryify good response
      .catch((error) => alert(error));
  }

  getCurrentUserDocs();

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

<div transition:fade class="container-fluid">
  <div class="row">
    <div class="col-2 border" style="height: 100vh">
      <div class="row justify-content-center " id="addTitles">
        {#each alldocs as doc, i}
          <button
            transition:fly={{ y: 200, duration: 1000 + i * 55 }}
            class="btn fw-bold mt-2 btn-primary col-10 rounded-pill"
            style="text-overflow: ellipsis; white-space: nowrap;
            overflow: hidden;;"
            on:click={() => updateCurrentDoc(i)}
          >
            {doc.title}
          </button>
        {/each}
      </div>
    </div>
    <form class="col-10" style="height: 100vh">
      <div class="row  mt-1">
        <div class="col-5">
          <input
            type="text"
            bind:value={title}
            id="title"
            placeholder="Title"
            class="form-control"
          />
        </div>
        <div class="col-7 ">
          <div class="row justify-content-end ">
            <div class="col-auto">
              <button
                class=" align-right btn-success btn"
                on:click={handleSaveForm}>save</button
              >
            </div>

            <div class="col-auto mr-1">
              <button class="btn-info btn me-4" id="new-file">new file</button>
            </div>
          </div>
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
    <div class="col" />
  </div>
</div>
