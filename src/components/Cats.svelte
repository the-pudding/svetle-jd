<script>
    let w;
    const catPlaces = [ 'car', 'porch', 'yard']
    const catTypes = [ 'tuxedo', 'gray tabby', 'orange tabby', 'black', 'calico', 'tortoise', 'gray and white', 'orange and white', 'white'];
    let randomCats;
    let foundCat = false;

    function getCats() {
        randomCats = catTypes.sort(() => Math.random() - Math.random()).slice(0, 3)
        console.log(randomCats)
    }

    function toggle() {
        foundCat = !foundCat;
    }

    function catClick() {
        let catID = this.id
        let catDiv = document.getElementById(catID)
        toggle();
        foundCat ? catDiv.classList.add('found') : catDiv.classList.remove('found')
    }

    getCats();
</script>

<section id='cats'>
    {#each catPlaces as place}
        <div class='row row-{place}'>
            <p>{place}</p>
            <div class='blocks'>
                {#each randomCats as randomCat,i}
                    <div on:click={catClick} bind:clientWidth={w} style='height: {w}px' class='cat' id='cat-{place}-{i}'>
                        <p>{randomCat}</p>
                    </div>
                {/each}
            </div>
        </div>
    {/each}
</section>

<style>
    #cats {
        max-width: 40rem;
        margin: 0 auto;
        border: 1px solid black;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
    }

    .row {
        width: 33%;
        text-align: center;
        display: flex;
        flex-direction: column;
    } 

    .cat {
        border: 1px solid black;
    }

    .found {
        background-color: gray;
    }
</style>