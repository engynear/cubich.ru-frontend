<svelte:head>
	<title>Кубыч</title>
</svelte:head>

<script>
	let showModal = false;
	let token = '';

	import { onMount } from 'svelte';
	import { tokenStore } from '../stores.js';
	import { goto } from "$app/navigation";

	onMount(async () => {
		tokenStore.useLocalStorage();
		tokenStore.subscribe((value) => {
			token = value;
		});
	});

	import LoginForm from '$lib/ui/LoginSingup.svelte';

	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();

	function handleClickScreen() {
		if (showModal) {
			showModal = false;
		}
	}
	
	function handlePlay() {
		if (token !== '') {
			goto('/profile');
		} else {
			showModal = true;
		}
	}

	function handleDone() {
		showModal = false;
		document.location.reload()
	}
</script>

<div class="root">
	{#if showModal}
		<div id="fullscreen" on:click={handleClickScreen}></div>
	{/if}
	<div id="home" class:blured="{showModal}">
		<div class="content">
			<h1>Ламповый <br/> Майнкрафт <br/> Сервер</h1>
			<p>Майнкрафт проект для друзей и их друзей <br/>
				Ванильное выживание, где главное - сиськи <br/>
				Но гриферить всё равно нельзя</p>
				<button on:click={handlePlay}>Играть</button>
		</div>
		<img src="wizard.png" alt="" class="wizard">
	</div>
	<div id="loginform" class:showed={showModal}>
		<LoginForm on:login={handleDone} on:register={handleDone}/>
	</div>
</div>




<style>
	.root {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: 100%;
	}
	#fullscreen {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: transparent;
		z-index: 1;
	}
	#loginform {
		visibility: hidden;
		position: fixed;
		top: 0px;
		left: 50%;
		transform: translate(-50%, 0);
		opacity: 0;
		transition: visibility 0.2s, opacity 0.2s linear;
		z-index: 2;
	}

	#loginform.showed {
		visibility: visible;
		opacity: 1;
	}
	#home {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		height: 100%;
		width: 100%;
		padding-top: 50px;
		transition: filter 0.2s linear;
	}

	#home.blured {
		filter:opacity(0.5);
		filter:blur(50px);
	}

	.wizard {
		margin-left: auto;
	}

	.content {
		padding: 0px 20px 0px 20%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: left;
	}

	.content h1 {
		text-align: left;
		color: white;
		font-size: 3.5rem;
		font-weight: 600;
		margin: 0;
		padding: 0;
	}

	.content p {
		text-align: left;
		color: white;
		font-size: 1.3rem;
		font-weight: 250;
	}

	button {
        background-color: #007073;
        color: white;
        font-weight: 500;
        font-size: 1.5rem;
        border: 0px solid;
        padding: 10px 20px 10px 20px;
        border-radius: 15px;
    }

</style>