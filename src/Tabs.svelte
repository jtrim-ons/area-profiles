<script>
    export let options;

    export let selected = options[0];

    let chooseOption = opt => { selected = opt };
</script>


    <div>
        <ul class="flex">
            {#each options as opt}
                <li class="m-0 p-0 mr-2"
                    class:border-grey1={selected === opt}
                    class:border={selected === opt}
                    class:border-b-0={selected === opt}
                    class:bg-white={selected === opt}
                    class:z-10={selected === opt}
                    class:-mb-px={selected === opt}>
                    <a class="py-2 px-4 block"
                       class:text-base={selected === opt}
                       class:font-bold={selected === opt}
                       class:m-0={selected === opt}
                       class:no-underline={selected === opt}
                       class:text-grey1={selected !== opt}
                       class:hover:text-grey1={selected !== opt}
                       class:bg-grey4={selected !== opt}
                       class:hover:bg-grey3={selected !== opt}
                       on:click={() => chooseOption(opt)}>{opt}</a>
                </li>
            {/each}
        </ul>
        <div class="border-t border-grey1 py-4 bg-white relative">
            <slot></slot>
        </div>
    </div>
