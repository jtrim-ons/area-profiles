<script>
    export let self;
    export let children;
</script>

<section class="wrapper mb-8 mt-4">
        <div class="mb-8" id="areas-within-yorkshire-and-the-humber">
            <header>
                <h2 class="text-md font-bold mb-4">Areas within {self.properties.name}</h2>
            </header>
            
<div>
    <ul class="flex">
        
            
                <li class="m-0 p-0 mr-2 -mb-px border-grey1 border border-b-0 bg-white z-10">
                    <h3 class="text-base font-bold m-0 py-2 px-4">Electoral Wards</h3>
                </li>
            
        
            
                <li class="m-0 p-0 mr-2">
                    <a href="" class="text-grey1 hover:text-grey1 bg-grey4 hover:bg-grey3 py-2 px-4 block">Statistical areas</a>
                </li>
            
        
            
                <li class="m-0 p-0 mr-2">
                    <a href="" class="text-grey1 hover:text-grey1 bg-grey4 hover:bg-grey3 py-2 px-4 block">Census areas</a>
                </li>
            
        
    </ul>
    <div class="border-t border-grey1 py-4 bg-white relative">
        
<ul class="list-none m-0 p-0 text-cols-2 md:text-cols-3 leading-relaxed">
    {#each children as child}
    <li class="m-0 p-0">
        <a href="#/{child.properties.code}">{child.properties.name}</a>
    </li>
    {/each}    
</ul>

    </div>
</div>
        </div>
        <div class="mb-8">
            <a class="text-base icon-arrow p-3 pl-2 border border-blumine border-opacity-30  leading-none" href="#top">Back to top</a>
        </div>
    </section>
