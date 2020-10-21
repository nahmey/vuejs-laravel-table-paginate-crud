<template>
    <div>
        <template>
            <filter-vue ref="filtreComponent"
            v-if="fl != false"
            v-bind:filters="fl"
            v-on:changeFilters="changeFilters"
            v-on:resetFilter="resetFilter"
            v-on:search="search"
            ></filter-vue>
        </template>
        <template>
            <div class="clearfix mb-4">
                <div class="float-right ml-4">
                    <select class="form-control" v-on:change="setPages($event), page = 1" v-model="perPage">
                        <option v-for="nb in nbPerPage" :value="nb" :key="'nbPerPage' + nb">
                            {{nb}}
                        </option>
                    </select>
                </div>
                <p class="text-primary float-right text-center mb-0 mr-4 align-middle">Votre recherche : {{countResult}} Résultat(s)</p>
                <custom-button-vue class="float-left" v-if="create_button"
                v-on:modalInfo="modalInfo"
                v-bind:classButton="create_button.class_button" 
                v-bind:text="create_button.text" 
                v-bind:action="create_button.action" 
                v-bind:icon="create_button.icon"
                v-bind:identifiant="create_button.id"
                v-bind:href="create_button.href"
                v-bind:url="create_button.url"
                v-bind:alert="create_button.alert"
                v-bind:modal="create_button.modal"
                v-on:destroyResult="destroy"
                v-on:createResult="create"
                ></custom-button-vue>
            </div>
        </template>
        <table class="table table-responsive-md table-striped">
            <thead class="thead">
                <tr>
                    <th v-for="column in col" v-bind:style="column.style" v-bind:class="column.class">
                        <div v-if="column.key != false">
                            <h5>
                                <a href="javascript:" v-on:click=" currentSortDir = currentSortDir === 'asc'?'desc':'asc', currentSort = column.key">
                                    {{column.title}} <i v-bind:class="sortOrder(column.key, currentSortDir)"></i>
                                </a>
                            </h5>
                        </div>
                        <div v-else>
                            <h5>
                                {{column.title}}
                            </h5>
                        </div>
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(data, index) in displayedDonnees" :key="data.id">
                    <td v-for="column in col" v-bind:class="column.class">
                        <div v-if="data[column.key]">
                            <span v-html="data[column.key]"></span>
                        </div>
                        <div v-else-if="column.button && column.button == true">
                            <custom-button-vue
                            v-on:modalInfo="modalInfo" 
                            v-bind:classButton="column.class_button" 
                            v-bind:text="column.text" 
                            v-bind:action="column.action" 
                            v-bind:icon="column.icon"
                            v-bind:identifiant="data.id"
                            v-bind:href="column.href"
                            v-bind:url="column.url"
                            v-bind:alert="column.alert"
                            v-bind:modal="column.modal"
                            v-on:destroyResult="destroy"
                            v-on:createResult="create"
                            ></custom-button-vue>
                        </div>
                    </td>
                </tr>
            </tbody>
        </table>

        <nav aria-label="Page navigation example" v-if="pages.length > 1">
            <ul class="pagination justify-content-center">
                <li class="page-item" v-if="page != 1">
                    <a class="page-link"  v-on:click.prevent="page--" href="#">Retour</a>
                </li>
                <li v-if="page != 1 && page > 5">
                    <a class="page-link page-scroll" v-on:click.prevent="page = 1" href="#">1</a>
                </li>
                <li v-if="page != 1 && page > 5" aria-disabled="true">
                    <a class="page-link page-scroll" href="javascript:">...</a>
                </li>
                <template v-for="pageNumber in paginationPage()">
                    <li :class="page == pageNumber ? 'page-item active' : 'page-item'">
                        <a class="page-link page-scroll" v-on:click.prevent="page = pageNumber" href="#">{{pageNumber}}</a>
                    </li>
                </template>
                <li v-if="page != numberOfPages && page < numberOfPages-5" aria-disabled="true">
                    <a class="page-link page-scroll" href="javascript:">...</a>
                </li>
                <li v-if="page != numberOfPages && page < numberOfPages-5 ">
                    <a class="page-link page-scroll" v-on:click.prevent="page = numberOfPages" href="#">{{numberOfPages}}</a>
                </li>
                <li class="page-item" v-if="page < pages.length">
                    <a class="page-link" v-on:click.prevent="page++" href="#">Suivant</a>
                </li>
            </ul>
        </nav>

        <modal-vue ref="modalComponent"
            v-bind:name="modal.name"
            v-bind:modalTitle="modal.modal_title"
            v-bind:modalInputs="modal.modal_inputs"
            v-bind:action= "modal.action"
            v-bind:url="modal.url"
            v-bind:method="modal.method"
            v-on:createResult="create"
            v-on:editResult="edit"
        ></modal-vue>
    </div>
</template>

<script>
    export default {
        name:  'PaginationVue',
        props: {
            columns: Array,
            results: Array,
            numberPerPage: Array,
            createButton: false,
            filters: Array,
        },
        data: function(){
            return{
                pages: [],
                currentSortDir: 'asc',
                currentSort: this.columns != false && this.columns[0] && this.columns[0].key ? this.columns[0].key : '',
                col: this.columns,
                res: this.results,
                page: 1,
                perPage: 10,
                nbPerPage: this.numberPerPage,
                create_button: this.createButton,
                showModal: false,
                fl: this.filters != undefined ? this.filters : false,
                filter: {},
                countResult: 0,
                numberOfPages: 0,
                searchBar: '',
                modal: {
                    name: '',
                    modalTitle: '',
                    modalInputs: '',
                    action: '',
                    url: '',
                    method: '',
                }
            }
        },
        mounted(){

        },
        watch: {
            res: function() {
                this.countResult = this.res.length;
                this.setPages();
            },
        },
        methods: {
            modalInfo: function(event){
                this.modal = {
                    name: event.name,
                    modalTitle: event.modal_title,
                    modalInputs: event.modal_inputs,
                    action: event.action,
                    url: event.url,
                    method: event.method,
                    identifiant: event.identifiant,
                }

                let element = [];
                if(event.action == 'edit'){
                    element = this.results.filter(function(item){
                        return item.id == event.identifiant;
                    });
                }

                this.$refs.modalComponent.showModal(this.modal, element);
            },
            sortOrder: function(tri, ordre){
                if(this.currentSort == tri && this.currentSortDir == 'asc') return 'fas fa-caret-up';
                if(this.currentSort == tri && this.currentSortDir == 'desc') return 'fas fa-caret-down';
            },
            setPages: function(event) {
                this.pages = [];
                let numberOfPages = Math.ceil(this.res.length / this.perPage);
                for (let index = 1; index <= numberOfPages; index++) {
                    this.pages.push(index);
                }

                this.numberOfPages = numberOfPages;
                // this.page = 1;
            },
            paginationPage: function(){
                let before = 1;
                if(this.page <= 5) before = this.page - this.page; 
                else before = this.page-5;
                return this.pages.slice(before, this.page+4);
            },
            sort: function(data) {
                let currentSort = this.currentSort;
                let currentSortDir = this.currentSortDir;
                let self = this;
                data.sort(function(a, b) { 
                    let modifier = 1;
                    if(self.currentSortDir === 'desc') modifier = -1;
                    return a[currentSort] > b[currentSort] ? 1*modifier : -1*modifier;
                });

                let from = (this.page * this.perPage) - this.perPage;
                let to = (this.page * this.perPage);
                return data.slice(from, to);
            },
            create(event){
                this.res.push(event);
                
            },
            edit(event){
                let element = this.res.map(function(x){
                    return x.id;
                }).indexOf(event.id);

                this.res.splice(element, 1, event);
            },
            destroy(event){
                let element = this.res.map(function(x){
                    return x.id;
                }).indexOf(event);

                this.res.splice(element, 1);
            },
            checkIfSearchBar: function(self){
                let merged = [];
                if(self.searchBar != ''){
                    let all_result = [];
                    self.columns.forEach(function(element){
                        if(element.key != false){
                            let result = [];
                            result = self.results.filter(function(item){
                                if(item[element.key] != undefined) return item[element.key].toLowerCase().includes(self.searchBar);
                            })
                            all_result.push(result);
                        }
                    })

                    merged = [].concat.apply([], all_result);
                    let obj = {};

                    for ( let i=0, len=merged.length; i < len; i++ ) obj[merged[i]['id']] = merged[i];

                    merged = new Array();
                    for ( let key in obj ) merged.push(obj[key]); 
                }else{
                    merged = self.results;
                }
                return merged;
            },
            search: function(event){
                let self = this;
                self.searchBar = event.toLowerCase();
                let merged = this.checkIfSearchBar(self);

                /*
                 * If select filter is active 
                 */
                let results = merged.filter(function(item){
                    for(var key in self.filter) {
                        if(item[key.toLowerCase()] === undefined || item[key.toLowerCase()] != self.filter[key.toLowerCase()]) return false;
                    }
                    return true;
                });

                this.res = results;
                this.page = 1;
            },
            changeFilters: function(event){
                var self = this;
                let merged = this.checkIfSearchBar(self);

                if(event.filter.type == 'select'){
                    if(event.value != 0) self.filter[event.name] = event.value;    
                    else delete self.filter[event.name];

                    self.res = merged.filter(function(item){
                        for(var key in self.filter) {
                            // OLD VERSION
                            // if(item[key.toLowerCase()] === undefined || item[key.toLowerCase()].trim() != self.filter[key.toLowerCase()].trim()) return false;
                            //
                            if(item[key.toLowerCase()] === undefined || item[key.toLowerCase()].trim() != self.filter[key.toLowerCase()].trim() || !item[key.toLowerCase()].trim().includes(self.filter[key.toLowerCase()].trim()) ) return false;
                        }
                        return true;
                    });

                }else if(event.filter.type == 'checkbox'){
                    self.filter[event.name] = event.value;

                    console.log(self.filter)

                    let result = [];
                    let cle = 0;

                    for(let checkbox in self.filter){
                        let donnee = self.results.filter(function(item){
                            for(var key in self.filter) {
                                if(item[key.toLowerCase()] === undefined || item[key.toLowerCase()] != self.filter[key.toLowerCase()]) return false;
                            }
                            return item.id;
                        });
                        console.log(checkbox)
                        cle++
                        result.push(donnee);
                    }

                    // console.log(result[0]);


                    // self.res = self.results.filter(function(item){
                    //     for(var key in checkbox){
                    //         if(item[key.toLowerCase()] === undefined || item[key.toLowerCase()] != self.filter[key.toLowerCase()]) return false;
                    //     }
                    //     return true;
                    // });

                    self.res = Object.values(result[0]);
                }
                this.page = 1;
            },
            resetFilter: function(event){
                this.filter = [];
                this.res = this.results;
                // this.$refs.modalComponent.showModal(this.modal, element);
            }
        },
        computed: {
            displayedDonnees: function() {
                return this.sort(this.res);
            },
        }
    }
</script>