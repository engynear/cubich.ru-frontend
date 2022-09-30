<script>
	import { page } from '$app/stores';
	import { tokenStore } from '../../stores';
	import logo from './logo.png';

	

	let name = 'Кубыч';

	const menus = [
    { label: 'Главная', href: '/' },
    { label: 'Карта', href: 'https://cubich.ru/map' },
    // { label: 'Правила', href: '/rules' },
    // { label: 'Контакты', href: '/contacts' },
  	];

	import { onMount } from 'svelte';

	let token = '';
	let user = {
		username: '',
		logged_in: false
	};

	onMount(async () => {
		tokenStore.useLocalStorage();
		tokenStore.subscribe((value) => {
			token = value;
		});
		
		console.log("Check token: " + token);
		if (token !== '') {
			const responce = await fetch('https://cubich.ru/auth/ping/', {
				method: 'POST',
				mode: 'cors',
				headers: {
					'Content-Type': 'application/json',
					'Authorization': `Bearer ${token}`
				}
			});
			
			if (responce.ok) {
				user.logged_in = true;
				responce.text().then((text) => {
					user.username = text;
				});
				console.log("Logged in as " + user.username);
			} else {
				console.log(responce.status);
				user.logged_in = false;
				user.username = '';
				tokenStore.set('');
				console.log("Unauthorized");
			}
		} else {
			console.log("No token");
		}
		
	});

	function handleExit() {
		tokenStore.set('');
		user.logged_in = false;
		user.username = '';

		console.log("Logged out");
	}
</script>

<header>
	<div class="left-corner corner">
		<a href="/" class="toplogo-a">
			<img src={logo} alt="" class="toplogo-img"/>
		</a>
		<p class="topname">{name}</p>
		<div class="separator"></div>
	</div>

	<nav data-sveltekit-prefetch>
		<ul>
			{#each menus as { label, href }}
				<li class:active={$page.url.pathname === href}>
					<a href={href} >
						{label}
					</a>
				</li>
			{/each}
		</ul>
	</nav>

	<div class="right-corner corner">
		{#if user.logged_in}
			<a href="/profile" class="topname">{user.username}</a>
			<div class="separator"></div>
			<a href="#" class="topname" on:click={handleExit}>Выйти</a>
		<!-- {:else}
			<a href="#" class="topname">Войти</a> -->
		{/if}
	</div>
</header>

<style>
	header {
		font-family: Montserrat;
		display: flex;
		justify-content: flex-start;
		width: 100%;
		padding: 0px 384px 0px 384px;
	}

	.topname {
		color: white;
		font-size: 1.3rem;
		margin: 5px;
		font-weight: 400;
		text-decoration: none;
		box-sizing: border-box;
	}

	.toplogo-a {
		width: 50px;
		height: 50px;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		flex-flow: column wrap;
		box-sizing: border-box;
	}

	.corner {
		/* width: 100%; */
		height: 100%;
		box-sizing: border-box;
	}

	.left-corner {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 100%;
		flex-direction: row;
	}

	.right-corner {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 100%;
		flex-direction: row;

		margin-left: auto;
	}

	.separator {
		border-left: 1px solid #b2b2b3;
		height: 25px;
		margin-left: 15px;
		margin-right: 15px;
	}

	.corner img {
		width: 2.5em;
		height: 2.5em;
		object-fit: contain;
		margin-right: 10px;
	}

	nav {
		display: flex;
		justify-content: center;
		background: transparent;
	}

	ul {
		position: relative;
		padding: 0;
		margin: 0;
		height: 3em;
		display: flex;
		justify-content: center;
		align-items: center;
		list-style: none;
		background: var(--background);
		background-size: contain;
	}

	li {
		position: relative;
		height: 100%;
		color: #b2b2b3;
	}

	li.active {
		color: white;
	}

	li.active::before {
		--size: 6px;
		content: '';
		width: 0;
		height: 0;
		position: absolute;
		top: 0;
		left: calc(50% - var(--size));
		border: var(--size) solid transparent;
		border-top: var(--size) solid white;
	}

	nav a {
		display: flex;
		height: 100%;
		align-items: center;
		padding: 0 1em;
		color: inherit;
		font-weight: 700;
		font-size: 0.8rem;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		text-decoration: none;
		transition: color 0.2s linear;
	}

	a:hover {
		color: white;
	}
</style>
