<script>
    // IMPORTS
    import {selection, select, selectAll} from "d3-selection";
    import { fly } from 'svelte/transition';
    import Confetti from "./Confetti.svelte";

    // VARIABLES
    let w;
    const catPlaces = [ 'on a car', 'on the porch', 'in the yard']
    const catTypes = [ 'tuxedo', 'gray tabby', 'orange tabby', 'black', 'calico', 'gray', 'gray & white', 'orange & white', 'white', 'brown tabby'];
    let bingo = false;
    let bingoColumn;
    let allColumns;

    // INTERACTIONS
    function catClick() {
        let catID = this.id
        let catDiv = document.getElementById(catID)
        catDiv.classList.contains('found') ? catDiv.classList.remove('found') : catDiv.classList.add('found');
        checkBingo();
    }

    function newCardClick() {
        allColumns = catPlaces.map(getCats)
        const foundCats = selectAll('.found') 
        foundCats.classed('found', false)
        bingo = false
        const bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', false)
        const bingoBoard = selectAll('#cats') 
        bingoBoard.classed('is-dimmed', false)
        const bingoBlocks = selectAll('.block') 
        bingoBlocks.classed('is-inactive', false)
    }

    function clearClick() {
        const foundCats = selectAll('.found')
        foundCats.classed('found', false)
        bingo = false
        const bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', false)
        const bingoBoard = selectAll('#cats') 
        bingoBoard.classed('is-dimmed', false)
        const bingoBlocks = selectAll('.block') 
        bingoBlocks.classed('is-inactive', false)
    }

    // SET UP BOARD
    function getCats(place, i) {
        bingoColumn = catTypes.sort(() => Math.random() - Math.random()).slice(0, 3)
        bingoColumn = [place, bingoColumn]
        return bingoColumn
    }
    allColumns = catPlaces.map(getCats)

    // BINGO
    function checkBingo() {
        let blockCar0 = document.getElementById('block-onacar-0').classList.contains('found')
        let blockCar1 = document.getElementById('block-onacar-1').classList.contains('found')
        let blockCar2 = document.getElementById('block-onacar-2').classList.contains('found')

        let blockPorch0 = document.getElementById('block-ontheporch-0').classList.contains('found')
        let blockPorch1 = document.getElementById('block-ontheporch-1').classList.contains('found')
        let blockPorch2 = document.getElementById('block-ontheporch-2').classList.contains('found')

        let blockYard0 = document.getElementById('block-intheyard-0').classList.contains('found')
        let blockYard1 = document.getElementById('block-intheyard-1').classList.contains('found')
        let blockYard2 = document.getElementById('block-intheyard-2').classList.contains('found')

        //bingo check
        //columns
        if (blockCar0 && blockCar1 && blockCar2) { revealBingo() }
        if (blockPorch0 && blockPorch1 && blockPorch2) { revealBingo() }
        if (blockYard0 && blockYard1 && blockYard2) { revealBingo() }
        //rows
        if (blockCar0 && blockPorch0 && blockYard0) { revealBingo() }
        if (blockCar1 && blockPorch1 && blockYard1) { revealBingo() }
        if (blockCar2 && blockPorch2 && blockYard2) { revealBingo() }
        //diagonals
        if (blockCar0 && blockPorch1 && blockYard2) { revealBingo() }
        if (blockCar2 && blockPorch1 && blockYard0) { revealBingo() }
    }

    function revealBingo() {
        bingo = true
        const bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', true)
        const bingoBoard = selectAll('#cats') 
        bingoBoard.classed('is-dimmed', true)
        const bingoBlocks = selectAll('.block') 
        bingoBlocks.classed('is-inactive', true)
    }
</script>

<section class='bingo'>
    {#if bingo}
        <p transition:fly="{{ y: 100, duration: 500 }}">BINGO!</p>
        <div class="confetti">
            <Confetti />
        </div>
    {/if}
</section>
<section id='cats'>
    <p class='header'>neighborhood cat bingo</p>
    <div class='board'>
        {#each allColumns as column, i}
        <div class='row row-{column[0].replace(/\s/g, '')}'>
            <p>{column[0]}</p>
            {#each column[1] as cat, i}
                <div on:click={catClick} bind:clientWidth={w} style='height: {w}px' class='block' id='block-{column[0].replace(/\s/g, '')}-{i}'>
                    <div class='img-wrapper'>
                        <img src="assets/imgs/{cat.replace(/\s/g, '')}.png" alt="{cat} cat">
                        {#if cat == 'black'}
                            <img class='whiskers' src="assets/imgs/gray-whiskers.png" alt="whiskers">
                        {:else if cat == 'tuxedo'}
                            <img class='whiskers' src="assets/imgs/gray-whiskers.png" alt="whiskers">
                        {:else}
                            <img class='whiskers' src="assets/imgs/black-whiskers.png" alt="whiskers">
                        {/if}   
                    </div>
                    <p>{cat}</p>
                </div>
            {/each}
        </div>
    {/each}
    </div>
</section>
<section id='controls'>
    <button on:click={newCardClick}>Get new card</button>
    <button on:click={clearClick}>Clear card</button>
</section>
<section id='credits'>
    <p>Made by <a href='https://twitter.com/jadiehm' target='_blank'>Jan</a>. Inspired by walks with <a href='https://twitter.com/sarahserp' target='_blank'>Sarah</a>. In memory of <a href='http://jadiehm.github.io/' target='_blank'>Rory</a> and in honor of <a href='https://www.instagram.com/woo.can.too/' target='_blank'>Woo</a>. Built while learning <a href='https://svelte.dev/' target='_blank'>Svelte</a>.</p>
</section>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Balsamiq+Sans&display=swap');

    .header {
        width: 100%;
        font-family: 'Balsamiq Sans', var(--sans);
        text-align: center;
        font-size: 2.5rem;
        text-transform: uppercase;
        margin: 0 0 2rem 0;
        color: var(--gray-dark);
    }

    .board {
        display: flex;
        flex-direction: row;
        width: 100%;
    }

    #cats {
        max-width: 40rem;
        margin: 2rem auto;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        background-color: var(--off-white);
        padding: 2rem;
        border-radius: 0.5rem;
        font-family: var(--sans);
        position: relative;
        transition: 1s ease-in-out;
    }

    .row {
        width: 33%;
        text-align: center;
        display: flex;
        flex-direction: column;
    } 

    .row p {
        text-transform: uppercase;
        font-weight: 700;
        font-size: 1.25rem;
        margin: 0;
        font-family: 'Balsamiq Sans', var(--sans);
    }

    .img-wrapper {
        width: 75%;
        height: 75%;
        position: relative;
    }

    .img-wrapper img {
        width: 100%;
        position: absolute;
    }

    .block {
        background-color: var(--white);
        border-radius: 0.5rem;
        margin: 0.5rem;
        position: relative;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }

    .block p {
        text-transform: none;
        font-weight: 500; 
        font-family: 'Balsamiq Sans', var(--sans);
        font-size: 1rem;
    }

    .found::before {
        position: absolute;
        content: '';
        font-size: 10rem;
        background-color:#8fd8f2;
        border-radius: 50%;
        width: 70%;
        height: 70%;
        margin: 0;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        mix-blend-mode: multiply;
        z-index: 1000;
    }

    #controls {
        max-width: 40rem;
        margin: 2rem auto 0 auto;
        display: flex;
        flex-direction: row;
        justify-content: center;
    }

    button {
        margin: 0 0.5rem;
        font-family: 'Balsamiq Sans', var(--sans);
    }

    .bingo {
        display: none;
        position: absolute;
        z-index: 1000;
        top: 40%;
        left: 50%;
        transform: translate(-50%, -50%);
        transition: 1s ease-in-out;
        font-family: 'Balsamiq Sans', var(--sans);
    }

    .is-visible {
        display: inline-block;
    }

    .is-dimmed {
        opacity: 0.15;
    }

    .is-inactive {
        pointer-events: none;
    }

    .bingo p {
        font-family: 'Balsamiq Sans', var(--sans);
        font-weight: 700;
        font-size: 10rem;
        color: #f93e19;
    }

    .confetti {
        position: absolute;
        top: 30%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    #credits {
        position: absolute;
        bottom: 0%;
        left: 50%;
        transform: translate(-50%, 0%);
        font-family: var(--sans);
        color: var(--gray-dark);
        font-size: 0.75rem;
        width: 100%;
        text-align: center;
        padding: 1rem;
    }
    .whiskers:hover {
        animation-name: tilt;
        animation-duration: 1s;
        animation-iteration-count: infinite;
    }

    @keyframes tilt {
        0%   {transform: rotate(0deg)}
        25%  {transform: rotate(3deg)}
        50%  {transform: rotate(-3deg)}
        100% {transform: rotate(0deg)}
    }

    @media only screen and (max-width: 640px) {
        #cats {
            padding: 0.5rem;
            margin: 0;
        }
        .header {
            font-size: 1.25rem;
            margin: 0.75rem 0;
        }

        .row p {
            font-size: 0.75rem;
        }

        .img-wrapper {
            width: 70%;
            height: 70%;
        }

        .block p {
            font-size: 0.75rem;
        }

        .bingo {
            top: 25%;
        }

        .bingo p {
            font-size: 4rem;
        }
    }
</style>