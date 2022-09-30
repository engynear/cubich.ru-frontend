<script>
// @ts-nocheck
    let token = '';

    import { onMount } from 'svelte';
    import { tokenStore } from '../../stores.js';

    onMount(async () => {
        tokenStore.useLocalStorage();
        tokenStore.subscribe((value) => {
            token = value;
        });
    });

    let files;
    async function submitForm(){
      const dataArray = new FormData();
      dataArray.append("file", files[0]);
      await fetch("https://cubich.ru/auth/UploadSkin/", {
        method: "POST",
        headers: {
                    'Accept': '*/*',
					'Authorization': `Bearer ${token}`
				},
        body: dataArray
      })
        .then(response => {
          console.log(response);
          response.text().then((text) => {
            console.log("TEXT: " + text);
          });
        })
        .catch(error => {
            console.log(error);
        });
    }
  </script>

{#if token !== ''}
    <div class="profile">
        <div>
            <form on:submit|preventDefault={submitForm}>
                <label class="custom-file-upload">
                    <input type="file" bind:files class='load-skin'/>
                    Загрузить скин
                </label>
                <p>{files}</p>
                <br />
                <input class="submit-button" type="submit" />
            </form>
        </div>
        <div class="download-steps">
            <li><a href="https://www.java.com/ru/download/manual.jsp">Установи Java 8 в автономном режиме</a></li>
            <li><a href="https://www.google.com/search?q=java+17+download&oq=java+17+download&aqs=chrome..69i57j0i512l9.4404j0j7&sourceid=chrome&ie=UTF-8">Установи Java 17+</a></li>
            <li><a href="https://launcher.cubich.ru/Launcher.exe">Установи лаунчер</a></li>
        </div>
    </div>
    
{:else}
    <div class="need-auth">
        <h1>Загрузка скина</h1>
        <p>Для загрузки скина необходимо авторизоваться</p>
        <a href="/" class="home">На главную</a>
    </div>
{/if}

<style>
    .profile {
        margin-top: 20px;
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-around;
        width: 100%;
        height: 100%;
    }

    li {
        color: white;
    }
    
    a {
        color: white;
        text-decoration: underline;
    }

    .need-auth {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        text-align: center;
    }
    
    .home {
        display: block;
        margin-top: 20px;
        padding: 10px 20px;
        background-color: #007073;
        color: white;
        border-radius: 5px;
        text-decoration: none;
    }

    .need-auth h1 {
        color: white;
    }

    .need-auth p {
        color: white;
    }

    form {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        width: 100%;
        height: 100%;
    }

    input[type="file"] {
        display: none;
    }

    

    .custom-file-upload {
        border: 1px dashed #ccc;
        display: inline-block;
        padding: 30px 90px;
        cursor: pointer;
        color: white;
        margin: 0;
    }

    .load-skin {
        display: flex;
        flex-direction: column;
    }

    .submit-button {
        margin: 0;
        background-color: #4CAF50;
        border: none;
        color: white;
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        margin: 4px 2px;
        cursor: pointer;
    }
</style>