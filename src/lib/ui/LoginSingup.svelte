<script>
    import { tokenStore } from '../../stores.js';
    import { onMount } from 'svelte';

    import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

    export let tokenValue = '';
    let shakeLoginButton = false;
    let shakeRegisterButton = false;
    let showResponce = false;
    /**
* @type {HTMLElement | null}
*/
    let loginComment;
    /**
* @type {HTMLElement | null}
*/
    let registerComment;

    onMount(async () => {
        tokenStore.useLocalStorage();
        tokenStore.subscribe((value) => {
            tokenValue = value;
        });

        loginComment = document.getElementById('login-comment');
        registerComment = document.getElementById('register-comment');
    });

    async function handleLogin() {
        shakeLoginButton = false;
        showResponce = false;
        // @ts-ignore
        const data = new FormData(document.querySelector('#login-form'));
        const user = data.get('logname');
        const pwd = data.get('logpass');

        const jso = JSON.stringify({
            userName: user,
            password: pwd
        })

        console.log(jso);
        let response = await fetch('https://cubich.ru/auth/login/', {
                    method: 'POST',
                    mode: 'cors',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: jso
            })

        if (response.ok) {
            console.log("OK");
            response.text().then((text) => {
                tokenStore.set(text);
                console.log(tokenValue);
            });
            dispatch('login', {text: `${user}`});
        } else {
            shakeLoginButton = true;

            if (response.status == 401) {
                // @ts-ignore
                loginComment.innerHTML = "Неверный логин или пароль";
            } else {
                // @ts-ignore
                loginComment.innerHTML = "Ошибка сервера";
            }

            showResponce = true;
        }
        
        console.log(response.status);
    }

    async function handleRegister() {
        shakeRegisterButton = false;
        showResponce = false;
        // @ts-ignore
        const data = new FormData(document.querySelector('#register-form'));
        const user = data.get('regname');
        const pwd = data.get('regpass');
        const pwdConfirm = data.get('regpassconfirm');
        console.log(pwd);

        const jso = JSON.stringify({
        userName: user,
        password: pwd,
        passwordConfirm: pwdConfirm
        })

        console.log(jso);
        let response = await fetch('https://cubich.ru/auth/register/', {
                    method: 'POST',
                    mode: 'cors',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: jso
            })
        
        console.log(response.status);

        if (response.ok) {
            console.log(`Registered ${user}`);
            const jsoLogin = JSON.stringify({
                userName: user,
                password: pwd
            })

            let responseLogin = await fetch('https://cubich.ru/auth/login/', {
                        method: 'POST',
                        mode: 'cors',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        body: jsoLogin
                })

            if (responseLogin.ok) {
                console.log("OK");
                responseLogin.text().then((text) => {
                    tokenStore.set(text);
                });
                dispatch('login', {text: `${user}`});
            } else {
                dispatch('register');
            }
        } else {
            shakeRegisterButton = true;

            if (response.status == 400) {
                showResponce = true;
                // @ts-ignore
                registerComment.innerHTML = 'Пароли не совпадают';
            } else if (response.status == 409) {
                showResponce = true;
                // @ts-ignore
                registerComment.innerHTML = 'Пользователь с таким именем уже существует';
            }

            showResponce = true;
        }
    }
</script>

<div class="section">
    <div class="container">
        <div class="row full-height justify-content-center">
            <div class="col-12 text-center align-self-center py-5">
                <div class="section pb-5 pt-5 pt-sm-2 text-center">
                    <h6 class="mb-0 pb-3"><span>Вход</span><span>Регистрация</span></h6>
                    <input class="checkbox" type="checkbox" id="reg-log" name="reg-log"/>
                    <label for="reg-log"></label>
                    <div class="card-3d-wrap mx-auto">
                        <div class="card-3d-wrapper">
                            <div class="card-front">
                                <div class="center-wrap">
                                    <form class="section text-center" id="login-form">
                                        <h4 class="mb-4 pb-3">Вход</h4>
                                        <div class="form-group">
                                            <input type="text" name="logname" class="form-style" placeholder="Никнейм" id="logname" autocomplete="off">
                                            <i class="input-icon uil uil-at"></i>
                                        </div>	
                                        <div class="form-group mt-2">
                                            <input type="password" name="logpass" class="form-style" placeholder="Пароль" id="logpass" autocomplete="off">
                                            <i class="input-icon uil uil-lock-alt"></i>
                                        </div>
                                        <a href="/" on:click={handleLogin} class="btn mt-4" class:shake-button={shakeLoginButton}>Войти</a>
                                        <p class="mb-0 mt-4 text-center"><a href="#0" class="link">Забыли пароль?</a></p>
                                        <p class="responce-comment" id="login-comment" class:show-comment={showResponce}>Даня гей</p>
                                    </form>
                                </div>
                            </div>
                            <div class="card-back">
                                <div class="center-wrap">
                                    <form class="section text-center" id="register-form">
                                        <h4 class="mb-4 pb-3">Регистрация</h4>
                                        <div class="form-group">
                                            <input type="text" name="regname" class="form-style" placeholder="Никнейм" id="regname" autocomplete="off">
                                            <i class="input-icon uil uil-user"></i>
                                        </div>	
                                        <div class="form-group mt-2">  
                                            <input type="password" name="regpass" class="form-style" placeholder="Пароль" id="regpass" autocomplete="off">
                                            <i class="input-icon uil uil-at"></i>
                                        </div>	
                                        <div class="form-group mt-2">
                                            <input type="password" name="regpassconfirm" class="form-style" placeholder="Повторите пароль" id="regpassconfirm" autocomplete="off">
                                            <i class="input-icon uil uil-lock-alt"></i>
                                        </div>
                                        <a href="/" on:click={handleRegister} class="btn mt-4" class:shake-button={shakeRegisterButton}>Зарегистрироваться</a>
                                        <p class="responce-comment" id="register-comment" class:show-comment={showResponce}>Тимоха гей</p>
                                    </form>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    @import url('https://fonts.googleapis.com/css?family=Poppins:400,500,600,700,800,900');

    body{
        font-family: Montserrat, sans-serif;
        font-weight: 300;
        font-size: 15px;
        line-height: 1.7;
        color: #c4c3ca;
        background-color: #1f2029;
        overflow-x: hidden;
    }
    a {
        cursor: pointer;
    transition: all 200ms linear;
    }
    a:hover {
        text-decoration: none;
    }
    .link {
    color: #c4c3ca;
    }
    .link:hover {
    color: #ffeba7;
    }
    p {
    font-weight: 500;
    font-size: 14px;
    line-height: 1.7;
    }
    h4 {
    font-weight: 600;
    }
    h6 span{
    padding: 0 20px;
    text-transform: uppercase;
    font-weight: 700;
    }
    .section{
    position: relative;
    width: 100%;
    display: block;
    color: #fff;
    }
    .full-height{
    min-height: 100vh;
    }
    [type="checkbox"]:checked,
    [type="checkbox"]:not(:checked){
    position: absolute;
    left: -9999px;
    }
    .checkbox:checked + label,
    .checkbox:not(:checked) + label{
    position: relative;
    display: block;
    text-align: center;
    width: 60px;
    height: 16px;
    border-radius: 8px;
    padding: 0;
    margin: 10px auto;
    cursor: pointer;
    background-color: #ffeba7;
    }
    .checkbox:checked + label:before,
    .checkbox:not(:checked) + label:before{
    position: absolute;
    display: block;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    color: #ffeba7;
    background-color: #102770;
    font-family: 'unicons';
    content: '';
    z-index: 20;
    top: -10px;
    left: -10px;
    line-height: 36px;
    text-align: center;
    font-size: 24px;
    transition: all 0.5s ease;
    }
    .checkbox:checked + label:before {
    transform: translateX(44px) rotate(-270deg);
    }


    .card-3d-wrap {
    position: relative;
    width: 440px;
    max-width: 100%;
    height: 400px;
    -webkit-transform-style: preserve-3d;
    transform-style: preserve-3d;
    perspective: 800px;
    margin-top: 60px;
    }
    .card-3d-wrapper {
    width: 100%;
    height: 100%;
    position:absolute;    
    top: 0;
    left: 0;  
    -webkit-transform-style: preserve-3d;
    transform-style: preserve-3d;
    transition: all 600ms ease-out; 
    }
    .card-front, .card-back {
    width: 100%;
    height: 100%;
    background-color: #2a2b38;
    background-image: url('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1462889/pat.svg');
    background-position: bottom center;
    background-repeat: no-repeat;
    background-size: 300%;
    position: absolute;
    border-radius: 6px;
    left: 0;
    top: 0;
    -webkit-transform-style: preserve-3d;
    transform-style: preserve-3d;
    -webkit-backface-visibility: hidden;
    -moz-backface-visibility: hidden;
    -o-backface-visibility: hidden;
    backface-visibility: hidden;
    }
    .card-back {
    transform: rotateY(180deg);
    }
    .checkbox:checked ~ .card-3d-wrap .card-3d-wrapper {
    transform: rotateY(180deg);
    }
    .center-wrap{
    position: absolute;
    width: 100%;
    padding: 0 35px;
    top: 50%;
    left: 0;
    transform: translate3d(0, -50%, 35px) perspective(100px);
    z-index: 20;
    display: block;
    }


    .form-group{ 
    position: relative;
    display: block;
        margin: 0;
        padding: 0;
    }
    .form-style {
    padding: 13px 20px;
    padding-left: 55px;
    height: 48px;
    width: 100%;
    font-weight: 500;
    border-radius: 4px;
    font-size: 14px;
    line-height: 22px;
    letter-spacing: 0.5px;
    outline: none;
    color: #c4c3ca;
    background-color: #1f2029;
    border: none;
    -webkit-transition: all 200ms linear;
    transition: all 200ms linear;
    box-shadow: 0 4px 8px 0 rgba(21,21,21,.2);
    }
    .form-style:focus,
    .form-style:active {
    border: none;
    outline: none;
    box-shadow: 0 4px 8px 0 rgba(21,21,21,.2);
    }
    .input-icon {
    position: absolute;
    top: 0;
    left: 18px;
    height: 48px;
    font-size: 24px;
    line-height: 48px;
    text-align: left;
    color: #ffeba7;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }

    .form-group input:-ms-input-placeholder  {
    color: #c4c3ca;
    opacity: 0.7;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input::-moz-placeholder  {
    color: #c4c3ca;
    opacity: 0.7;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input:-moz-placeholder  {
    color: #c4c3ca;
    opacity: 0.7;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input::-webkit-input-placeholder  {
    color: #c4c3ca;
    opacity: 0.7;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input:focus:-ms-input-placeholder  {
    opacity: 0;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input:focus::-moz-placeholder  {
    opacity: 0;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input:focus:-moz-placeholder  {
    opacity: 0;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }
    .form-group input:focus::-webkit-input-placeholder  {
    opacity: 0;
    -webkit-transition: all 200ms linear;
        transition: all 200ms linear;
    }

    .btn{  
    border-radius: 4px;
    height: 44px;
    font-size: 13px;
    font-weight: 600;
    text-transform: uppercase;
    -webkit-transition : all 200ms linear;
    transition: all 200ms linear;
    padding: 0 30px;
    letter-spacing: 1px;
    display: -webkit-inline-flex;
    display: -ms-inline-flexbox;
    display: inline-flex;
    -webkit-align-items: center;
    -moz-align-items: center;
    -ms-align-items: center;
    align-items: center;
    -webkit-justify-content: center;
    -moz-justify-content: center;
    -ms-justify-content: center;
    justify-content: center;
    -ms-flex-pack: center;
    text-align: center;
    border: none;
    background-color: #ffeba7;
    color: #102770;
    box-shadow: 0 8px 24px 0 rgba(255,235,167,.2);
    }
    .btn:active,
    .btn:focus{  
    background-color: #102770;
    color: #ffeba7;
    box-shadow: 0 8px 24px 0 rgba(16,39,112,.2);
    }
    .btn:hover{  
    background-color: #102770;
    color: #ffeba7;
    box-shadow: 0 8px 24px 0 rgba(16,39,112,.2);
    }

    .shake-button {
        animation: shake 0.82s cubic-bezier(.36,.07,.19,.97) both;
        transform: translate3d(0, 0, 0);
        perspective: 1000px;
    }

    .responce-comment {
        font-size: 14px;
        font-weight: 500;
        color: #ffeba7;
        text-align: center;
        margin-top: 10px;
        display: none;
    }

    .show-comment {
        display: block;
    }
    
    @keyframes shake {
        10%, 90% {
            transform: translate3d(-1px, 0, 0);
        }
        20%, 80% {
            transform: translate3d(2px, 0, 0);
        }
        30%, 50%, 70% {
            transform: translate3d(-4px, 0, 0);
        }
        40%, 60% {
            transform: translate3d(4px, 0, 0);
        }
    }
</style>