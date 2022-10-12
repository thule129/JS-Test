<template>
    <div class="content" ref="content" v-if="store">
        <div class="content-header" ref="header">{{store.name}} Menu</div>
        <div class="filter-bar" ref="filterBar">
            <MButton
                text="Filter"
                color="blue"
                @click="filterBtnOnClick"
            />
            <div class="dropdown-holder">
                <MDropdown
                    label="Sort By"
                    :items="this.sortCategories"
                    @selectDropdown = "setSortType"
                />
            </div>  
        </div>
        <div class="select-bar" v-if="showSelectBar" ref="selectBar">
            <b>Toppings:</b>
            <div class="select-list">
                <div class="check-item" v-for="(filterType, index) in filterCategories" :key="index">
                    <MCheckbox @setCheckbox="setFilterType(index, $event)"/>
                    <div class="check-item-name">{{filterType.text}}</div>
                </div>
            </div>
        </div> 
        <div class="products-list scrollable" ref="productsList">
            <div class="products-list-holder">
                <div class="product" v-for="(product, index) in products" :key="index">
                    <div>MT-{{index+1}}</div>
                    <div><b>{{product.name}}</b></div>
                    <div class="p-seperate"></div>
                    <div>
                        <span><b>Toppings:</b></span>
                        <span>{{product.toppings}}</span>
                    </div>
                    <div class="p-price">${{product.price}}</div>
                </div>
            </div>
        </div> 
    </div>
</template>
<script>
import MButton from '../components/MButton.vue'
import MDropdown from '../components/MDropdown.vue'
import MCheckbox from '../components/MCheckbox.vue'
import Stores from '../data/stores.json'
import StoreProducts from '../data/storeProducts.json'
import Products from '../data/products.json'

export default {
    name: "TheContent",
    components: {
        MButton, MDropdown, MCheckbox
    }, 
    props:{
        storeIndex:{
            default: null,
            type: Number,
        },
    },
    data(){
        return{
            sortCategories:[
                {
                    text: 'Name',
                    id: '0'
                },
                {
                    text: 'Price',
                    id: '1'
                }
            ],
            filterCategories:[
                {
                    text: 'Milk Foam',
                    id: '0'
                },
                {
                    text: 'Pearl',
                    id: '1'
                },
                {
                    text: 'Aloe',
                    id: '0'
                }
            ],
            showSelectBar: false,
            stores: [],
            store: null,
            originProducts: [],
            products: [],
            processData: {
                sortType: null,
                filterTypes: new Set()
            },
            processSignal: 0,
        }
    },
    methods:{
        filterBtnOnClick(){
            var plus = 0
            if(!this.showSelectBar){
                this.showSelectBar = !this.showSelectBar
                var long = this.$refs.productsList.offsetHeight
                this.$nextTick(()=>{
                    plus = this.$refs.selectBar.offsetHeight
                    this.$refs.productsList.style.setProperty('height', long - plus +'px', 'important')
                })
            }
            else{
                var short = this.$refs.productsList.offsetHeight
                plus = this.$refs.selectBar.offsetHeight
                this.$refs.productsList.style.setProperty('height', short + plus +'px', 'important')
                this.showSelectBar = !this.showSelectBar
            }
        },
        getProductIds(shopId){
            console.log(shopId)
            var productIds = new Set()
            var storeProductsList = StoreProducts.shopProducts
            storeProductsList.forEach(pair => {
                if(pair.shop == shopId){
                    productIds.add(pair.product)
                }
            });
            return productIds
        },
        getProducts(shopId){
            var productIds = this.getProductIds(shopId)
            var productsList = Products.products
            productsList.forEach(product => {
                if(productIds.has(product.id)){
                    this.products.push(product)
                }
            });
            this.originProducts = this.products
        },
        setSortType(sortId){
            this.processData.sortType = sortId 
            this.processSignal += 1
            // console.log(this.processData.sortType)
        },
        setFilterType(checkIndex, item){
            // console.log(this.$refs.selectBar.querySelectorAll('.checkbox.checked'))
            // console.log(item.classList)
            if(item.classList.contains('checked')){
                this.processData.filterTypes.add(checkIndex)
            }else{
                this.processData.filterTypes.delete(checkIndex)
            }
            this.processSignal -= 1
            // console.log(this.processData)
        },
        sortProduct(list, sortType){
            if(sortType == 0){
                //sort theo tên
                return list.slice().sort(this.sortByName)
            }
            if(sortType == 1){
                //sort theo price
                return list.slice().sort(this.sortByPrice)
            }else return list
        },
        sortByName(a, b){
            var nameA = a.name.toLowerCase(), nameB = b.name.toLowerCase();
            if (nameA < nameB) //sort string ascending
                return -1;
            if (nameA > nameB)
                return 1;
            return 0
        },
        sortByPrice(a, b){
            var priceA = a.price, priceB = b.price;
            if (priceA < priceB) //sort string ascending
                return -1;
            if (priceA > priceB)
                return 1;
            return 0
        },
        filterProduct(list, filterTypes){
            var result = new Set()
            if(filterTypes.size == 0) return list
            else{
                filterTypes.forEach(index => {
                    var filterText = this.filterCategories[index].text.toLocaleLowerCase()
                    list.forEach(p => {
                        if(p.toppings.toLocaleLowerCase().includes(filterText)){
                            result.add(p)
                        }
                     });
                });
                return Array.from(result)  
            }
        },
        // filterByText(products, text){
        //     var result = new Set()
        //     products.forEach(p => {
        //         if (p.name.includes(text)){
        //             result.add(p)
        //         }
        //     });
        // }

    },
    created(){
        this.stores = Stores.stores
    },
    watch: {
        storeIndex: function(){
            this.store = this.stores[this.storeIndex]
            this.products = []
            this.getProducts(this.store.id)
            this.processSignal += 1
            // this.processData = null
        },
        processSignal(){
            this.products = this.filterProduct(this.originProducts, this.processData.filterTypes)
            console.log(this.products)
            this.products = this.sortProduct(this.products, this.processData.sortType)
        },
        showSelectBar(){
            if(this.showSelectBar){
                this.$nextTick(()=>{
                    var checkboxesList = this.$refs.selectBar.querySelectorAll('.checkbox')
                    console.log(checkboxesList)
                    checkboxesList.forEach((checkbox, index) => {
                        if(this.processData.filterTypes.has(index)){
                            checkbox.classList.add('checked')
                        }
                    });
                })
                
            }
            console.log(this.showSelectBar)
           
        }
    }

}
</script>
<style scoped>
    @import url('../styles/content.css')
</style>