<script>
    import ProgressCircle from "./ProgressCircle.svelte";
    import {createEventDispatcher} from "svelte";

    export let groups;
    export let disabled;
    let rounded = false;
    let registrered = false;

    var dispatch = createEventDispatcher();

    function select(group)
    {
        if(!registrered && document.getElementById("input").value.length > 0) {
            dispatch("register", {
                id: group.id,
                group: group
            });

            document.getElementsByName(group.id)[0].style.backgroundColor = "#3fbe6f";
            registrered = true;
        }
    }

    function email_change()
    {
        dispatch("username_changed", {
            username: document.getElementById("input").value
        });
    }

    function klasse_change()
    {
        dispatch("klasse_changed", {
            klasse: document.getElementById("input_klasse").value
        });
    }

    setInterval(() => {
        console.log(disabled);
    }, 1000);

</script>



{#if !rounded}
    <div class="container" style="border-radius: 16px; background-color: #232229;">
        {#if !disabled}
            <input id="input" on:change={email_change} type="text" placeholder="Navn" required/>
            <input id="input_klasse" on:change={klasse_change} type="text" placeholder="Klasse" required/>
        {/if}
        {#each groups as group}
            <div class="con">
                <div><ProgressCircle {group} {rounded}/></div>
                {#if !disabled}
                    <div class="button" name={group.id} on:click={() => {
                        select(group);
                    }}><h1>VELG</h1></div>
                {/if}
            </div>
        {/each}
    </div>
 {:else}
    <div class="container">
        {#if !disabled}
            <input id="input" on:change={email_change} type="text" placeholder="Navn" required/>
            <input id="input_klasse" on:change={klasse_change} type="text" placeholder="Klasse" required/>
        {/if}
        {#each groups as group}
            <div class="con">
                <div><ProgressCircle {group} {rounded}/></div>
                {#if !disabled}
                    <div class="button" name={group.id} on:click={() => {
                        select(group);
                    }}><h1>VELG</h1></div>
                {/if}
            </div>
        {/each}
    </div>
{/if}


<style>

    #input {
        width: 65%;
        margin: 20px 20px 20px 43px;
        background-color: #3D3E49;
        border: none;
        border-radius: 12px;
        font-size: 30px;
        color: white;
        padding: 1px 20px;
        font-weight: 400;
    }

    #input_klasse {
        width: 20%;
        margin: 20px 0px;
        background-color: #3D3E49;
        border: none;
        border-radius: 12px;
        font-size: 30px;
        color: white;
        padding: 1px 20px;
        font-weight: 400;
    }

    .container {
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        max-width: calc(240px*3);
        padding: 10px;
        margin: 10px;
    }

    .con {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .button {
        display: flex;
        flex-direction: column;
        border-radius: 30px;
        background-color: #3D3E49;
        min-width: 100px;
        padding: 5px 15px;
        width: fit-content;
        flex-wrap: wrap;
        height: 60px;
        justify-content: center;
        align-items: center;
        margin: 10px;
        user-select: none;
    }

    .button:hover {
        cursor: pointer;
    }

    .checked {
        background-color: #3fbe6f;
        user-select: none;
    }

    h1 {
        font-weight: 400;
        font-size: 30px;
        color: white;
    }

    div {
        display: flex;
        flex-direction: row;
    }

    @media (max-width: 760px) {
        .container {
            flex-direction: column;
            padding: 28px;
        }
    }
</style>