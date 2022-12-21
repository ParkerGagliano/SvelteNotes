<script>
  import { select_option, time_ranges_to_array } from "svelte/internal";
  import { get } from "svelte/store";
  import { fade } from "svelte/transition";
  import { fly } from "svelte/transition";
  const docSide = document.getElementById("addTitles");
  export let token;
  export let user_id;
  let isediting = false;
  let content = "";
  let title = "";
  let alldocs = [0];
  let currentdoc = 0;
  let message = "";
  let tone = "";
  let mode = "primary";
  let modemessage = "";

  $: if (isediting == true) {
    mode = "primary";
    modemessage = "Edit";
  } else {
    mode = "success";
    modemessage = "Add";
  }

  function updateCurrentDoc(i) {
    content = alldocs[i].content;
    title = alldocs[i].title;
    currentdoc = i;
    isediting = true;
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
      .then((result) => updateAllLocalDocs(result))
      .catch((error) => console.log("error", error));
  }
  function handleDeleteForm() {
    let myHeaders = new Headers();
    myHeaders.append("Authorization", token);
    let urlencoded = new URLSearchParams();
    console.log(alldocs[currentdoc].id);
    console.log(alldocs);
    urlencoded.append("id", alldocs[currentdoc].id);
    let requestOptions = {
      method: "DELETE",
      headers: myHeaders,
      body: urlencoded,
    };
    fetch(`http://127.0.0.1:3000/api/docs/owner`, requestOptions)
      .then((response) => response.text())
      .then((result) => updateAllLocalDocs(result))
      .catch((error) => console.log("error", error));
  }
  function updateAllLocalDocs(data) {
    console.log(data);
    if (isediting == false) {
      if (alldocs[0] == 0) {
        alldocs = data;
      } else {
        alldocs = [...alldocs, data];
        isediting = true;
        handleMessage("Added to server");
      }
    } else {
      if (data == "Document deleted") {
        alldocs = [
          ...alldocs.slice(0, currentdoc),
          ...alldocs.slice(currentdoc + 1),
        ];
        isediting = true;
        handleMessage("Deleted Successfully");
      } else {
        alldocs[currentdoc] = data;
        handleMessage(`Edited ${alldocs[currentdoc].title} Successfully`);
      }
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

  function handleSaveForm() {
    if (title == "") {
      handleError("Title cannot be blank");
      return;
    }
    if (isediting == true) {
      handleEditForm();
    } else {
      handleAddForm();
    }
  }
  function handleAddForm() {
    let myHeaders = new Headers();
    let urlencoded = new URLSearchParams();
    urlencoded.append("title", title);
    urlencoded.append("content", content);
    urlencoded.append("owner_id", user_id);
    myHeaders.append("Content-Type", "application/x-www-form-urlencoded");
    myHeaders.append("Authorization", token);
    let requestOptions = {
      method: "POST",
      headers: myHeaders,
      body: urlencoded,
    };
    fetch(`http://127.0.0.1:3000/api/docs`, requestOptions)
      .then((response) => response.json())
      .then((result) => updateAllLocalDocs(result)) //need more here to veryify good response
      .catch((error) => alert(error));
    currentdoc = alldocs.length;
  }
  getCurrentUserDocs();
  function handleEditForm() {
    let myHeaders = new Headers();
    let urlencoded = new URLSearchParams();
    console.log(currentdoc);
    console.log(alldocs[currentdoc].id);
    urlencoded.append("id", alldocs[currentdoc].id);
    urlencoded.append("title", title);
    urlencoded.append("content", content);
    urlencoded.append("owner_id", user_id);
    myHeaders.append("Content-Type", "application/x-www-form-urlencoded");
    myHeaders.append("Authorization", token);
    let requestOptions = {
      method: "PATCH",
      headers: myHeaders,
      body: urlencoded,
    };
    fetch(`http://127.0.0.1:3000/api/docs/owner`, requestOptions)
      .then((response) => response.json())
      .then((result) => updateAllLocalDocs(result)) //need more here to veryify good response
      .catch((error) => alert(error));
  }
  function newFile() {
    title = "";
    content = "";
    isediting = false;
  }
</script>

<div transition:fade class="container-fluid">
  {#if message != ""}
    <div
      transition:fly={{ y: -200, duration: 500 }}
      style="position: absolute; margin-left: 11vw; top: 0; min-width: 10vw;"
      class="row justify-content-center bg-{tone} mt-1 rounded-pill"
    >
      <div class="col-auto">
        <h4 class="m-0 p-0">{message}</h4>
      </div>
    </div>
  {/if}
  <div class="row justify-content-center mr-5 ">
    <div class="col-auto ">
      <h5 class="rounded-pill p-2 bg-{mode}">Mode: {modemessage}</h5>
    </div>
  </div>
  <div class="row">
    <div class="col-2 border">
      <div class="row justify-content-center  " id="addTitles">
        {#each alldocs as doc, i}
          {#if doc.name != "undefined"}
            <button
              transition:fly={{ y: 200, duration: 1000 + i * 55 }}
              class="btn fw-bold mt-2 btn-primary col-10 rounded-pill"
              style="text-overflow: ellipsis; white-space: nowrap;
            overflow: hidden;;"
              on:click={() => updateCurrentDoc(i)}
            >
              {doc.title}
            </button>
          {/if}
        {/each}
      </div>
    </div>
    <form class="col-10" style="height: 85">
      <div class="row justify-content-between mt-1">
        <div class="col-12 col-lg-5">
          <input
            class="text-bgs rounded"
            type="text"
            bind:value={title}
            id="title"
            placeholder="Title"
            style="width: 96%; height:100%; border: none; "
          />
        </div>
        <div class="col-lg-auto col-12">
          <div class="row justify-content-around ">
            <div class="col-auto mt-1">
              <button
                class=" align-right btn-success btn"
                on:click={handleSaveForm}>Save</button
              >
            </div>
            <div class="col-auto mt-1">
              <button
                class=" align-right btn-danger btn"
                on:click={handleDeleteForm}>Delete</button
              >
            </div>

            <div class="col-auto mr-1 mt-1">
              <button on:click={newFile} class="btn-info btn me-4" id="new-file"
                >New File</button
              >
            </div>
          </div>
        </div>
      </div>
      <div class="row" style=" width: 100%">
        <div class="col">
          <textarea
            placeholder="Content"
            rows="32"
            bind:value={content}
            class="mt-2 text-bgs rounded"
            id="main-text"
            type="text"
            style="width: 100%; outline: none; resize: none; border: none
            "
          />
        </div>
      </div>
    </form>
    <div class="col" />
  </div>
</div>
