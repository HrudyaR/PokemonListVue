<template>
  <div class="title">
        <p>Switch between Load More and Pagination here:</p>
        <button class="btn btn-primary" @click="selectType" ref="loadMore">Load More</button>
        <button class="btn" @click="selectType" ref="pagination">Pagination</button>
  </div>
  <div v-if="isLoadMore" class="list-container">
    <ul id="example-1">
      <li v-for="(item, index) in pokemonList" :key="item.name">
        <img :src=item.sprites.front_shiny alt="">
        <p>Item: {{index + 1}}</p>
        <p>Name: {{ item.species.name }}</p>
        <p>Weight: {{ item.weight }}</p>
        <p>Height: {{ item.height }}</p>
      </li>
    </ul>
  </div>
  <div v-if="isPagination" class="list-container">
    <ul id="example-1">
      <li v-for="(item, index) in displayedItems" :key="item.name">
        <img :src=item.sprites.front_shiny alt="">
        <p>Item: {{index + 1}}</p>
        <p>Name: {{ item.species.name }}</p>
        <p>Weight: {{ item.weight }}</p>
        <p>Height: {{ item.height }}</p>
      </li>
    </ul>
  </div>
  <div class="navigation">
      <div v-if="isLoadMore">
        <button @click="loadMoreItems" class="btn btn-primary" ref="loadMoreBtn">Load More...</button>
    </div>
    <div v-if="isPagination">
        <nav aria-label="Page navigation">
			<ul class="pagination">
				<li class="page-item">
					<button type="button" v-if="page !== 1" class="btn btn-light" @click="goToPrevious()"> Previous </button>
				</li>
				<li class="page-item">
					<button type="button" :class="(page === pageNumber)? 'btn-primary' : 'btn-light'" :key="pageNumber" class="btn" v-for="pageNumber in pages" @click="goToPage(pageNumber)"> {{pageNumber}} </button>
				</li>
				<li class="page-item">
					<button type="button" @click="goToNext()" v-if="page < pages.length" class="btn btn-light"> Next </button>
				</li>
			</ul>
		</nav>	
    </div>
  </div>
</template>

<script>

const axios = require('axios').default;

export default {
    name: 'PokemonList',
    data() {
        return {
            title: "Pokemon List!!",
            listLimit: 50,
            listOffset: 0,
            pokemonList: [],
            pageOfItems: [],
            selectedType: '',
            isLoadMore: true,
            isPagination: false,
            page: 1,
            perPage: 30,
            pages: []
        }
    },
    
    //Methods
    methods: {
    
    //Get list of pokemon from the api using axios
        getPokemonList() {
            if(this.listOffset > 150) {
                this.listLimit = 150 - this.pokemonList.length;
                this.$refs.loadMoreBtn.classList.add('disabled');
            }
            console.log(this.pokemonList.length);
            axios
            .get('https://pokeapi.co/api/v2/pokemon?limit=' + this.listLimit + '&offset=' + this.listOffset)
            .then(response => {
                const list = response.data.results;
                list.map((element) => {
                axios
                    .get(element.url)
                    .then(res => {
                        this.pokemonList.push(res.data);
                        console.log(this.pokemonList.length);
                    });
                });
            })
        },

        //Click on Load More button to load next 15 items
        loadMoreItems() {
            this.listOffset = this.pokemonList.length + 15;
            this.listLimit = 15;
            this.getPokemonList();
            
        },

        //Disaply 30 item per page
        paginate (pokemonList) {
            let page = this.page;
            let perPage = this.perPage;
            let from = (page * perPage) - perPage;
            let to = (page * perPage);
            return  pokemonList.slice(from, to);
        },

        //Set number of pages for pagination
        setPages () {
            this.pages = [];
            let numberOfPages = Math.ceil(this.listLimit / this.perPage);
            for (let index = 1; index <= numberOfPages; index++) {
                this.pages.push(index);
            }
        },

        //Go to specific page
        goToPage(pageNumber) {
            this.page = pageNumber;
        },

        goToPrevious() {
            this.page--;
        },

        goToNext() {
            this.page++;
        },

        //Select display type: Pagination or Load More
        selectType(event) {
            this.$refs.pagination.classList.remove('btn-primary');
            this.$refs.loadMore.classList.remove('btn-primary');
            event.target.classList.add('btn-primary');
            this.selectedType = event.target.textContent;
            this.pokemonList = [];
            if(this.selectedType === 'Load More') {
                this.isLoadMore = true;
                this.isPagination = false;
                this.listLimit = 50;
                this.listOffset = 0;
                this.getPokemonList();
            } else if(this.selectedType === 'Pagination') {
                this.isPagination = true;
                this.isLoadMore = false;
                this.listLimit = 150;
                this.listOffset = 0;
                this.getPokemonList();
                this.setPages();
            }
        },
    },
    created () {
        this.getPokemonList();
    },
    computed: {
		displayedItems () {
			return this.paginate(this.pokemonList);
		}
	},
}
</script>

<style scoped>
p {
    margin: 0;
    margin-bottom: 5px;
}
    .navigation {
        margin-bottom: 30px;
    }
    .list-container ul {
        list-style-type: none;
        display: inline-flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: space-between;
    }
    .list-container ul li {
        font-size: 14px;
        padding-top: 15px;
        padding-bottom: 15px;
        height: 100px;
        text-align: center;
        background-color: #fff;
        width: 250px;
        margin: 10px 10px 20px;
        box-shadow: 0px 0px 6px -2px rgb(20 72 62);
        cursor: pointer;
        transition: all ease-in-out 0.3s;
        text-align: right;
        padding-right: 20px;
    }
    .list-container ul li img {
        transition: all ease-in-out 0.3s;
        float: left;
    }
    .list-container ul li:hover {
        box-shadow: 0px 0px 21px -7px rgb(20 72 62);
    }
    .list-container ul li:hover img {
        transform: scale(1.3);
    }
    .disabled {
        opacity: 0.5;
        pointer-events: none;
    }
    button.page-link {
        display: inline-block;
    }
    button.page-link {
        font-size: 20px;
        color: #29b3ed;
        font-weight: 500;
    }
    .offset{
    width: 500px !important;
    margin: 20px auto;  
    }
    .pagination {
        list-style-type: none;
    }
    .pagination li {
        display: inline-block;
    }
</style>