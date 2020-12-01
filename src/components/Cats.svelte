<script>
    import {selection, select, selectAll} from "d3-selection";
    let w;
    const catPlaces = [ 'car', 'porch', 'yard']
    const catTypes = [ 'tuxedo', 'gray tabby', 'orange tabby', 'black', 'calico', 'gray', 'gray and white', 'orange and white', 'white', 'brown tabby'];
    let bingoColumn;
    let allColumns;
    let foundCat = false;

    function getCats(place, i) {
        bingoColumn = catTypes.sort(() => Math.random() - Math.random()).slice(0, 3)
        bingoColumn = [place, bingoColumn]
        return bingoColumn
    }

    function toggle() {
        foundCat = !foundCat;
    }

    function checkBingo() {
        let blockCar0 = document.getElementById('block-car-0').classList.contains('found')
        let blockCar1 = document.getElementById('block-car-1').classList.contains('found')
        let blockCar2 = document.getElementById('block-car-2').classList.contains('found')

        let blockPorch0 = document.getElementById('block-porch-0').classList.contains('found')
        let blockPorch1 = document.getElementById('block-porch-1').classList.contains('found')
        let blockPorch2 = document.getElementById('block-porch-2').classList.contains('found')

        let blockYard0 = document.getElementById('block-yard-0').classList.contains('found')
        let blockYard1 = document.getElementById('block-yard-1').classList.contains('found')
        let blockYard2 = document.getElementById('block-yard-2').classList.contains('found')

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
        console.log('bingo')
        let bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', true)
    }

    function catClick() {
        let catID = this.id
        let catDiv = document.getElementById(catID)
        toggle();
        foundCat ? catDiv.classList.add('found') : catDiv.classList.remove('found')
        checkBingo();
    }

    function newCardClick() {
        allColumns = catPlaces.map(getCats) 
        let foundCats = selectAll('.found')
        foundCats.classed('found', false)
        let bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', false)
    }

    function clearClick() {
        let foundCats = selectAll('.found')
        foundCats.classed('found', false)
        let bingoText = selectAll('.bingo')
        bingoText.classed('is-visible', false)
    }

    allColumns = catPlaces.map(getCats)
   console.log(allColumns)
</script>

<section id='cats'>
    {#each allColumns as column, i}
        <div class='row row-{column[0]}'>
            <p>{column[0]}</p>
            {#each column[1] as cat, i}
                <div on:click={catClick} bind:clientWidth={w} style='height: {w}px' class='block' id='block-{column[0]}-{i}'>
                    <img src="assets/imgs/{cat.replace(/\s/g, '')}.png" alt="{cat} cat">
                    <p>{cat}</p>
                </div>
            {/each}
        </div>
    {/each}
</section>
<section id='controls'>
    <button on:click={newCardClick}>Get new card</button>
    <button on:click={clearClick}>Clear card</button>
</section>
<section class='bingo'>
   <p>BINGO!</p>
</section>

<style>
    #cats {
        max-width: 40rem;
        margin: 0 auto;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        background-color: var(--off-white);
        padding: 2rem;
        border-radius: 0.5rem;
        font-family: var(--sans);
    }

    .row {
        width: 33%;
        text-align: center;
        display: flex;
        flex-direction: column;
    } 

    .row p {
        text-transform: capitalize;
        font-weight: 700;
        margin: 0;
    }

    .row img {
        width: 75%;
    }

    .block {
        background-color: #fffbb3;
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
    }

    .found {
        //background-color: gray;
    }

    .found::before {
        position: absolute;
        content: 'X';
        font-size: 10rem;
	    color:red;
        margin: 0;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
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
    }

    .bingo {
        display: none;
        position: absolute;
        z-index: 1000;
        top: 40%;
        left: 50%;
        transform: translate(-50%, -50%);
    }

    .is-visible {
        display: inline-block;
    }

    .bingo p {
        font-family: var(--sans);
        font-weight: 700;
        font-size: 10rem;
    }
</style>