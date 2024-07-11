<script>
	export let name;
	export let image;
	export let link;

	let hover = false;
</script>

<div class="app-card" on:mouseover={()=>{hover = true}} on:mouseout={()=>{ hover = false }}>
	{#if hover}<div class="name">{name}</div>{/if}
	<a href='{link}'>
		<img src="{image}" alt="#"/>
	</a>
</div>

<style>
    .app-card {
        width: clamp(0px, 100%, 300px);
				height: 200px;
				border: 1px solid var(--header);
				position: relative;
				user-select: none;
    }

		a {
				width: 0;
				height: 0;
		}

    .app-card img{
        width: 100%;
        height: 100%;
    }

		.app-card img:hover{
        opacity: .1;
		}

    .name {
        color: red;
        position: absolute;
        right: 0;
        bottom: 0;
        margin: 10px;
    }
</style>